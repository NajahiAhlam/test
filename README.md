Yep, classic (and very real) problem \U0001f604
You want **automatic field-level history** on update: detect *what changed*, get **old value / new value**, and call `updateDossierHistory(...)` for each change.

I\u2019ll give you **clean + scalable solutions**, from **best practice** to **quick win**, and you can pick.

---

## \u2705 BEST PRACTICE (clean & future-proof)

### 1\ufe0f\u20e3 Clone entity BEFORE mapping, then compare AFTER mapping

You already use `ModelMapper`, so we\u2019ll:

* Load existing dossier
* Make a **copy snapshot**
* Map DTO \u2192 entity
* Compare snapshot vs updated entity
* Save history per changed field

---

### \U0001f539 Step 1 \u2013 Create a utility to compare fields (Reflection-based)

```java
public class EntityDiffUtil {

    public static Map<String, Object[]> diff(Object oldObj, Object newObj) {
        Map<String, Object[]> changes = new HashMap<>();

        for (Field field : oldObj.getClass().getDeclaredFields()) {
            field.setAccessible(true);

            try {
                Object oldValue = field.get(oldObj);
                Object newValue = field.get(newObj);

                if (!Objects.equals(oldValue, newValue)) {
                    changes.put(field.getName(), new Object[]{oldValue, newValue});
                }

            } catch (IllegalAccessException e) {
                throw new RuntimeException(e);
            }
        }
        return changes;
    }
}
```

---

### \U0001f539 Step 2 \u2013 Update service with history tracking

```java
@Transactional
public DossierDto updateDossier(Long id, DossierDto dossierDto) {

    Dossier existing = dossierRepository.findById(id)
            .orElseThrow(() -> new RuntimeException("Dossier introuvable"));

    // 1\ufe0f\u20e3 Snapshot BEFORE update
    Dossier oldSnapshot = new Dossier();
    modelMapper.map(existing, oldSnapshot);

    // 2\ufe0f\u20e3 Apply updates
    modelMapper.map(dossierDto, existing);

    // 3\ufe0f\u20e3 Detect changes
    Map<String, Object[]> changes =
            EntityDiffUtil.diff(oldSnapshot, existing);

    // 4\ufe0f\u20e3 Save history per field
    changes.forEach((field, values) -> {
        dossierHistoryService.updateDossierHistory(
                existing,
                "UPDATE_" + field.toUpperCase(),
                String.valueOf(values[0]),
                String.valueOf(values[1])
        );
    });

    Dossier saved = dossierRepository.save(existing);
    return modelMapper.map(saved, DossierDto.class);
}
```

\U0001f4a5 Result:

* Every changed field is logged
* Old value + new value saved
* Works even if you add 50 new fields later

---

## \u2705 OPTIONAL FILTERING (VERY IMPORTANT)

You probably **don\u2019t want to track everything** (id, dates, relations, audit fields).

Add this inside the `diff()` loop:

```java
if (field.isAnnotationPresent(OneToMany.class)
    || field.isAnnotationPresent(ManyToOne.class)
    || field.getName().equals("id")
    || field.getName().equals("histories")
    || field.getName().equals("events")
) {
    continue;
}
```

---

## \u2705 CLEANER ACTION NAMES

Instead of `UPDATE_NUMCONTRAT`, you can do:

```java
.action("Modification du champ : " + field)
```

or map labels:

```java
Map<String, String> labels = Map.of(
    "numContrat", "Numéro de contrat",
    "numChassis", "Numéro de châssis"
);
```

---

## \U0001f680 EVEN BETTER (ENTERPRISE STYLE)

If you want **zero logic in service**:

* Use **JPA EntityListener**
* Capture `@PreUpdate`
* Compare old/new automatically

\U0001f449 I can show you that version too if you want a **Spring Boot pro-grade solution**.

---

## \U0001f9e0 TL;DR

\u2714 Snapshot entity
\u2714 Map DTO
\u2714 Diff fields via reflection
\u2714 Save history per change

If you want:

* exclude nulls
* group changes in ONE history record
* save JSON diff instead of per field
* or auto-track status changes only

Just tell me \U0001f609
