Ahh got it \U0001f44d
You **don\u2019t want one history row per field**, you want **ONE single history row per update**, containing **only the fields that actually changed**. Clean audit trail. Love it.

Let\u2019s do it properly.

---

## \U0001f3af What you want (exact behavior)

On update:

* Detect changed fields \u2705
* Ignore unchanged fields \u2705
* Save **ONE row** in `dossier_history`
* `valueBefore` \u2192 concatenation (or JSON) of old values
* `valueAfter` \u2192 concatenation (or JSON) of new values

---

## \u2705 Recommended format (clean & readable)

### Example stored in DB

**valueBefore**

```
numContrat=12345; numChassis=ABC123
```

**valueAfter**

```
numContrat=67890; numChassis=XYZ999
```

(Or JSON if you prefer \u2014 I\u2019ll show both)

---

## \u2705 SOLUTION 1 \u2014 Simple & readable (String-based)

### \U0001f539 Update service (ONE history call only)

```java
@Transactional
public DossierDto updateDossier(Long id, DossierDto dossierDto) {

    Dossier existing = dossierRepository.findById(id)
            .orElseThrow(() -> new RuntimeException("Dossier introuvable"));

    Dossier oldSnapshot = new Dossier();
    modelMapper.map(existing, oldSnapshot);

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

\u2705 Only changed fields
\u2705 One DB insert
\u2705 Easy to read in prod

---

## \u2705 SOLUTION 2 \u2014 PRO / JSON-based (best for long term)

If `value_before` and `value_after` are `TEXT` (they are \U0001f44d), JSON is \U0001f525.

### \U0001f539 Build JSON maps

```java
if (!changes.isEmpty()) {

    Map<String, Object> before = new HashMap<>();
    Map<String, Object> after = new HashMap<>();

    changes.forEach((field, values) -> {
        before.put(field, values[0]);
        after.put(field, values[1]);
    });

    history.updateDossierHistory(
            existing,
            "UPDATE_DOSSIER",
            new ObjectMapper().writeValueAsString(before),
            new ObjectMapper().writeValueAsString(after)
    );
}
```

\U0001f4cc Result in DB:

```json
{"numContrat":"12345","numChassis":"ABC123"}
```

\U0001f4a1 Perfect if later you want:

* history UI
* diff highlighting
* audit export

---

## \U0001f525 IMPORTANT: Exclude noise fields (VERY IMPORTANT)

In `EntityDiffUtil.diff()` add this:

```java
if (field.getName().equals("dateModification")
 || field.getName().equals("createdDate")
 || field.getName().equals("createdBy")
 || field.getName().equals("modifiedBy")
 || field.getName().equals("histories")
 || field.getName().equals("events")
) {
    continue;
}
```

Otherwise you\u2019ll log useless updates every time.

---

## \U0001f9e0 Final result

\u2714 One history line per update
\u2714 Only changed fields
\u2714 Clean audit trail
\u2714 Zero manual field handling

If you want next:

* track **status change only**
* generate a **human-readable message**
* or move this logic into **@EntityListener (@PreUpdate)**

Say the word \U0001f604
