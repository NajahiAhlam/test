You're skipping this row **because both `code` and `designation` are empty**, but in your Excel the text *\u201cFrais de déplacement\u201d* is visually centered between the two columns \u2014 meaning **it's inside *one single cell*** (probably the *designation* column), but your code is reading it as empty.

\u2714\ufe0f **This happens when your Excel uses \u201cmerge cells\u201d** or the text is not in the column you expect.

---

# \u2705 **Fix: Accept rows where only `designation` is filled**

If you want to import rows that have **NO code but HAVE a designation**, then modify your condition.

### \u274c Current logic (too strict)

```java
if ((code == null || code.isEmpty()) && (designation == null || designation.isEmpty()) ) {
    skipped.add(new SkippedRow(i + 1, "Code et désignation manquants"));
    continue;
}
```

This skips anything without a code, even if designation exists.

---

# \u2705 **Correct logic**

\U0001f449 Skip only if **both code AND designation are missing**
\U0001f449 Import if **designation exists**, even when code is empty

Use this:

```java
boolean missingCode = (code == null || code.trim().isEmpty());
boolean missingDesignation = (designation == null || designation.trim().isEmpty());

if (missingCode && missingDesignation) {
    skipped.add(new SkippedRow(i + 1, "Code et désignation manquants"));
    continue;
}
```

This will **import** a row like:

| Code    | Designation          | Unite | Quantité |
| ------- | -------------------- | ----- | -------- |
| (empty) | Frais de déplacement | KM    | 660      |

---

# \U0001f9ea If Excel has merged cells

Merged cells in Apache POI return **the value only in the top-left cell** of the merged region.

If Excel merges col 0 + col 1, POI returns value only for column 0 (code).
Column 1 (designation) will appear empty.

If your merged cell is supposed to be in *designation*, you can shift it:

### Fix for merged regions (optional)

```java
if ((designation == null || designation.isEmpty()) && (code != null && !code.isEmpty())) {
    // Move code into designation if designation is empty
    designation = code;
    code = "";
}
```

---

# \u2714\ufe0f FINAL BEHAVIOR

Your problematic row **WILL be imported**.

If this is not enough and you want me to detect the merged cell automatically, send me **a screenshot of the Excel row** and I\u2019ll adjust the code.
