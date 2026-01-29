Perfect \U0001f44d thanks for the clarification \u2014 that actually makes it **simpler**.

\U0001f449 You **don\u2019t need another class**, you\u2019ll just **enhance `EntityDiffUtil` itself** so it converts
`camelCase` \u2192 **proper French label** at the moment you build the history.

No extra Java files. No annotations. No refactor.

---

## \u2705 What we\u2019ll do

Inside **`EntityDiffUtil`**:

1. Keep using the **field name as the key** (logic stays intact)
2. Add a small **formatter method**:

   ```
   villeRc \u2192 Ville RC
   numContrat \u2192 Numéro contrat
   dateDebut \u2192 Date début
   ```
3. Use that formatter when displaying history

---

## \u2705 Step 1: Add this method to `EntityDiffUtil`

```java
private static String toFrenchLabel(String fieldName) {

    // Split camelCase
    String label = fieldName
            .replaceAll("([a-z])([A-Z])", "$1 $2")
            .toLowerCase();

    // Capitalize first letter
    label = label.substring(0, 1).toUpperCase() + label.substring(1);

    // Small French corrections (OPTIONAL)
    label = label
            .replace("num ", "Numéro ")
            .replace("immat ", "Immatriculation ")
            .replace("ville ", "Ville ")
            .replace("date ", "Date ")
            .replace("rc", "RC");

    return label;
}
```

\u26a0\ufe0f This method lives **inside the SAME class**.

---

## \u2705 Step 2: Change what you store in `changes`

Replace this \U0001f447

```java
changes.put(
    field.getName(),
    new Object[]{oldValue, newValue}
);
```

With this \U0001f447

```java
String label = toFrenchLabel(field.getName());

changes.put(
    label,
    new Object[]{oldValue, newValue}
);
```

Do this in **both places** where `changes.put(...)` is called.

---

## \u2705 FINAL `EntityDiffUtil` (FULL CODE)

```java
public final class EntityDiffUtil {

    private static final ZoneId APP_ZONE = ZoneId.of("Europe/Paris");

    private static final Set<String> IGNORED_FIELDS = Set.of(
            "id",
            "createdBy",
            "modifiedBy",
            "createdDate",
            "dateModification",
            "histories",
            "events"
    );

    public static Map<String, Object[]> diff(Object oldObj, Object newObj) {

        Map<String, Object[]> changes = new LinkedHashMap<>();

        for (Field field : oldObj.getClass().getDeclaredFields()) {
            field.setAccessible(true);

            if (IGNORED_FIELDS.contains(field.getName())) {
                continue;
            }

            try {
                Object oldValue = normalize(field.get(oldObj));
                Object newValue = normalize(field.get(newObj));

                String label = toFrenchLabel(field.getName());

                if (oldValue instanceof LocalDateTime
                        && newValue instanceof LocalDateTime) {

                    Instant oldInstant =
                            ((LocalDateTime) oldValue)
                                    .atZone(APP_ZONE)
                                    .toInstant();

                    Instant newInstant =
                            ((LocalDateTime) newValue)
                                    .atZone(APP_ZONE)
                                    .toInstant();

                    if (!oldInstant.equals(newInstant)) {
                        changes.put(label, new Object[]{oldValue, newValue});
                    }
                    continue;
                }

                if (!Objects.equals(oldValue, newValue)) {
                    changes.put(label, new Object[]{oldValue, newValue});
                }

            } catch (IllegalAccessException e) {
                throw new RuntimeException(
                        "Error diffing field: " + field.getName(), e
                );
            }
        }
        return changes;
    }

    private static Object normalize(Object value) {
        if (value instanceof String) {
            String s = ((String) value).trim();
            return s.isEmpty() ? null : s;
        }
        return value;
    }

    private static String toFrenchLabel(String fieldName) {

        String label = fieldName
                .replaceAll("([a-z])([A-Z])", "$1 $2")
                .toLowerCase();

        label = label.substring(0, 1).toUpperCase() + label.substring(1);

        label = label
                .replace("num ", "Numéro ")
                .replace("immat ", "Immatriculation ")
                .replace("ville ", "Ville ")
                .replace("date ", "Date ")
                .replace("rc", "RC");

        return label;
    }
}
```

---

## \u2705 Result in history (exactly what you want)

```
Ville RC=Paris;
Numéro contrat=CN-123;
Date début=01/01/2026;
```

No camelCase
No extra class
Clean French display \U0001f1eb\U0001f1f7

---

## \u26a0\ufe0f Small warning (important)

Because you now store **labels as map keys**, you **can\u2019t rely on field names anymore later**.

\U0001f449 This is **perfect for display**, but if later you want:

* structured audit
* JSON diff
* API usage

Tell me and I\u2019ll show you the **dual-key approach** (fieldName + label).

If you want next:

* Better French grammar
* Abbreviations (`RC`, `WW`, `ICE`)
* Section headers (Véhicule / Crédit)

I\u2019ve got you \U0001f604
