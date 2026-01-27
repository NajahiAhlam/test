Perfect, thanks for the clarification \U0001f44d
This changes **everything** \u2014 and your requirement is 100% valid.

\U0001f449 **DTO contains ALL attributes**
\U0001f449 You still want **history to contain ONLY what actually changed**

So **ModelMapper is NOT the problem** \u274c
The solution is **diff smarter**, not map smarter.

---

## \u2705 FINAL CORRECT STRATEGY

We will:

1. Snapshot entity **before update**
2. Apply DTO (full update)
3. Compare **value-by-value**
4. Keep ONLY fields where `old != new`
5. Save **ONE history row**

Your code structure stays the same \u2014 only the diff logic must be strict.

---

## 1\ufe0f\u20e3 FINAL `EntityDiffUtil` (robust & correct)

This version:
\u2714 ignores unchanged values
\u2714 ignores same strings, numbers, enums
\u2714 ignores audit & relations
\u2714 works even if DTO sends all fields

```java
import java.lang.reflect.Field;
import java.util.HashMap;
import java.util.Map;
import java.util.Objects;

public final class EntityDiffUtil {

    private static final Set<String> IGNORED_FIELDS = Set.of(
            "id",
            "createdBy",
            "modifiedBy",
            "createdDate",
            "dateModification",
            "histories",
            "events"
    );

    private EntityDiffUtil() {}

    public static Map<String, Object[]> diff(Object oldObj, Object newObj) {

        Map<String, Object[]> changes = new HashMap<>();

        if (oldObj == null || newObj == null) {
            return changes;
        }

        for (Field field : oldObj.getClass().getDeclaredFields()) {
            field.setAccessible(true);

            if (IGNORED_FIELDS.contains(field.getName())) {
                continue;
            }

            try {
                Object oldValue = field.get(oldObj);
                Object newValue = field.get(newObj);

                // \U0001f525 ONLY REAL CHANGES
                if (!Objects.equals(oldValue, newValue)) {
                    changes.put(field.getName(), new Object[]{oldValue, newValue});
                }

            } catch (IllegalAccessException e) {
                throw new RuntimeException("Error comparing field " + field.getName(), e);
            }
        }
        return changes;
    }
}
```

\U0001f4cc `Objects.equals()` already handles:

* `null vs null` \u2192 same
* `"A" vs "A"` \u2192 same
* `1 vs 1` \u2192 same
* enum vs enum \u2192 same

---

## 2\ufe0f\u20e3 Your `updateDossier` (NO CHANGE NEEDED)

```java
@Transactional
public DossierDto updateDossier(Long id, DossierDto dossierDto) {

    Dossier existing = dossierRepository.findById(id)
            .orElseThrow(() -> new RuntimeException("Dossier introuvable"));

    // snapshot BEFORE update
    Dossier oldSnapshot = new Dossier();
    modelMapper.map(existing, oldSnapshot);

    // full update (DTO contains all fields)
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

## 3\ufe0f\u20e3 REAL-LIFE RESULT (exactly what you want)

### Front sends FULL DTO:

```json
{
  "numContrat": "123",
  "numChassis": "NEW123",
  "numWW": "WW999",
  "typeClient": "PRO"
}
```

### DB already had:

```
numContrat=123
numChassis=OLD999
numWW=WW111
typeClient=PRO
```

### History saved:

**value_before**

```
numChassis=OLD999; numWW=WW111;
```

**value_after**

```
numChassis=NEW123; numWW=WW999;
```

\u2714 Only changed fields
\u2714 One history row
\u2714 DTO full update supported
\u2714 No false positives

---

## \U0001f525 EXTRA (recommended for banking/audit)

If you want to **exclude technical changes** like spaces or case:

```java
if (oldValue instanceof String && newValue instanceof String) {
    if (((String) oldValue).trim().equals(((String) newValue).trim())) {
        continue;
    }
}
```

---

## \U0001f9e0 Final takeaway

\u27a1\ufe0f **DTO content doesn\u2019t matter**
\u27a1\ufe0f Only **entity-before vs entity-after** matters
\u27a1\ufe0f Your history is now **accurate, minimal, and audit-proof**

If you want:

* human-readable labels
* status transition history
* grouped history by business action

Just say the word \U0001f44c
