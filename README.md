Perfect \u2014 that means **your "Frais de déplacement" row is being processed BEFORE the special case**, so it gets treated as:

* `code = "Frais de déplacement"` \u274c
* `designation = ""` \u274c
* and then you get the wrong values.

To fix this **100%**, we must detect the merged cell **correctly**.

\U0001f449 In your Excel, the merged row looks like this:

| N°                                | DESIGNATION | U  | \u2026  |   |
| --------------------------------- | ----------- | -- | -- | - |
| (merged) **Frais de déplacement** |             | KM | 50 | \u2026 |

\U0001f449 That means in your sheet:

* `row.getCell(COL_CODE)` \u2192 contains **"Frais de déplacement"**
* `row.getCell(COL_DESIGNATION)` \u2192 is **null or empty**

So you MUST detect the special case using **code, not designation**.

---

# \u2705 PERFECT FIX

Detect \u201cFrais de déplacement\u201d using **code column**, NOT designation column.

Replace:

```java
if (designation.equalsIgnoreCase("Frais de déplacement"))
```

with:

```java
boolean isFrais = code.equalsIgnoreCase("Frais de déplacement") 
               || designation.equalsIgnoreCase("Frais de déplacement");
```

Then put this block **right after reading code/designation**, before ANY other validation:

```java
String code = safeTrim(getCellValue(row.getCell(COL_CODE)));
String designation = safeTrim(getCellValue(row.getCell(COL_DESIGNATION)));
String unite = safeTrim(getCellValue(row.getCell(COL_UNITE)));

boolean isFrais = code.equalsIgnoreCase("Frais de déplacement")
               || designation.equalsIgnoreCase("Frais de déplacement");

// SPECIAL CASE: FRAIS DE DÉPLACEMENT (MERGED CELL)
if (isFrais) {

    if (lastValidCode == null) {
        skipped.add(new SkippedRow(i + 1, "Impossible de générer N° pour Frais de déplacement"));
        continue;
    }

    // Generate dynamic new code
    double newCode = lastValidCode + 0.01;
    code = String.format("%.2f", newCode);

    // Force correct designation
    designation = "Frais de déplacement";

    lastValidCode = newCode;

    // Continue to normal article processing BELOW
}
```

---

# \U0001f9e0 WHY THIS WORKS

Your Excel row is:

```
| Frais de déplacement |   | KM | 50 | \u2026
```

So:

* `code = "Frais de déplacement"` \u2714
* `designation = ""` \u274c (empty because the Excel cells were merged)

Therefore detection **must be done on CODE**, not designation.

---

# \U0001f3af RESULT YOU WILL GET

Row before:

```
900.11   | Travaux | m2 | \u2026
```

Your "Frais de déplacement" row becomes:

```
code = 900.12
designation = "Frais de déplacement"
unite = KM
price columns = OK
```

\u2714 Inserted correctly
\u2714 No more skipped
\u2714 No more wrong mapping
\u2714 No more "designation null"

---

# \U0001f525 If you want, I can rewrite **your full method with the corrected logic inserted**.
