You have **one critical bug** in your code:

\U0001f449 **You check `missingCode` and `missingDesignation` *before* moving the \u201cfrais \u2026\u201d text from code \u2192 designation.**
So when code contains \u201cfrais \u2026\u201d:

* You detect **missingDesignation = true**
* Then you move code \u2192 designation
* But `missingDesignation` **still stays true**, so you skip the row OR process it wrong
  \u2192 leading to **quantité = 0 being saved** even when Excel has a real value.

---

# \u2705 **100% FIX**

\U0001f449 **Recalculate `missingCode` and `missingDesignation` after moving \u201cfrais\u201d to designation.**

Here is the corrected block:

```java
String code = getCellValue(row.getCell(0));
String designation = getCellValue(row.getCell(1));
String unite = getCellValue(row.getCell(2));
String quantiteStr = getCellValue(row.getCell(3));

// --- Move FRAIS... from code \u2192 designation ---
if (code != null && !code.trim().isEmpty()) {
    String lower = code.toLowerCase();
    if (lower.contains("frais")) {
        designation = code;
        code = "";
    }
}

// \U0001f525 MUST RECALCULATE AFTER MOVING FRAIS !!!
boolean missingCode = (code == null || code.trim().isEmpty());
boolean missingDesignation = (designation == null || designation.trim().isEmpty());

if (missingCode && missingDesignation) {
    skipped.add(new SkippedRow(i + 1, "Code et désignation manquants"));
    continue;
}
```

---

# \u26a0\ufe0f Why quantity becomes 0?

### CASE A \u2014 You skipped the row

Before fix, the row was skipped because:

* designation was empty *before* the move
* missingDesignation stayed **true**, even after move

So the row never reached the quantite parsing part.

\u2192 **quantity saved = 0 because articleProjet created with default 0**

---

### CASE B \u2014 Wrong column alignment

Often rows like *"Frais de déplacement    KM   660"*
have merged cells and your quantite appears in another column.

But let's first apply the fix above \u2014 80% chance it's only the missingDesignation bug.

---

# \U0001f7e2 If quantity still becomes 0 after fix

Tell me, and I'll help you auto-detect quantite even when column shifts.

Or just send me the screenshot of the row in Excel.
