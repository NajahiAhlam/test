Yep \U0001f44d you\u2019re **very close**, and your instinct is right:
what\u2019s happening now is **ModelMapper is overwriting fields with `null`**, so the diff sees *everything* as changed.

So the fix is **NOT in the diff**, it\u2019s in **how you map the DTO \u2192 entity**.

---

## \u274c Root cause (important)

Your DTO update probably looks like this:

```json
{
  "numChassis": "ABC123",
  "numWW": "WW999"
}
```

But `ModelMapper.map(dossierDto, existing)` does this:

\U0001f449 sets **all other fields to null**
\U0001f449 diff detects *all fields changed*
\U0001f449 history saves everything

---

## \u2705 CORRECT SOLUTION (industry standard)

### \U0001f7e2 Ignore `null` values during update

Only fields **explicitly provided** in the DTO will be updated \u2192 diff works perfectly.

---

## 1\ufe0f\u20e3 Configure ModelMapper ONCE

```java
@Bean
public ModelMapper modelMapper() {
    ModelMapper mapper = new ModelMapper();

    mapper.getConfiguration()
            .setSkipNullEnabled(true)  // \U0001f525 THIS IS THE KEY
            .setFieldMatchingEnabled(true)
            .setFieldAccessLevel(org.modelmapper.config.Configuration.AccessLevel.PRIVATE);

    return mapper;
}
```

\U0001f4cc This means:

* `null` in DTO \u2192 **does NOT overwrite entity**
* unchanged fields stay untouched
* diff sees ONLY real changes

---

## 2\ufe0f\u20e3 Your `updateDossier` stays EXACTLY the same

```java
@Transactional
public DossierDto updateDossier(Long id, DossierDto dossierDto) {

    Dossier existing = dossierRepository.findById(id)
            .orElseThrow(() -> new RuntimeException("Dossier introuvable"));

    Dossier oldSnapshot = new Dossier();
    modelMapper.map(existing, oldSnapshot);

    // \u2705 Now only changed fields are updated
    modelMapper.map(dossierDto, existing);

    Map<String, Object[]> changes =
            EntityDiffUtil.diff(oldSnapshot, existing);

    if (!changes.isEmpty()) {

        StringBuilder before = new StringBuilder();
        StringBuilder after = new StringBuilder();

        changes.forEach((field, values) -> {
            before.append(field)
                    .append("=")
                    .append(values[0])
                    .append("; ");

            after.append(field)
                    .append("=")
                    .append(values[1])
                    .append("; ");
        });

        history.updateDossierHistory(
                existing,
                "UPDATE_DOSSIER",
                before.toString(),
                after.toString()
        );
    }

    Dossier saved = dossierRepository.save(existing);
    return modelMapper.map(saved, DossierDto.class);
}
```

---

## \u2705 RESULT (what you wanted)

### You update ONLY:

```json
{
  "numChassis": "ABC123",
  "numWW": "WW999"
}
```

### History saved:

**value_before**

```
numChassis=OLD123; numWW=WW111;
```

**value_after**

```
numChassis=ABC123; numWW=WW999;
```

\U0001f4a5 Nothing else appears
\U0001f4a5 One history row
\U0001f4a5 Perfect audit

---

## \U0001f525 EXTRA SAFETY (optional but recommended)

Exclude fields that should never be audited:

```java
if (values[0] == null && values[1] == null) {
    return;
}
```

Or exclude empty strings:

```java
if ("".equals(values[0]) && "".equals(values[1])) {
    return;
}
```

---

## \U0001f9e0 TL;DR

\u2714 Problem was **ModelMapper null overwrite**
\u2714 Fix = `setSkipNullEnabled(true)`
\u2714 Diff logic already correct
\u2714 History now logs ONLY changed fields

If you want next:

* PATCH vs PUT handling
* validation per updated field
* status transition history only

I\u2019ve got you \U0001f604
