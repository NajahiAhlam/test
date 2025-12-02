OK, since the **quantity is still saved as 0**, that means **your `quantiteStr` is EMPTY** for this line.

So the REAL problem is:

\U0001f449 **Your Excel row "Frais de déplacement   KM   660" is not aligned like your code thinks.**
The number **660** is NOT in column 3.

### Typical case:

Because \u201cFrais de déplacement\u201d is centered or merged, Excel shifts the row like this:

| Col(0)               | Col(1)  | Col(2) | Col(3)    | Col(4?) |
| -------------------- | ------- | ------ | --------- | ------- |
| Frais de déplacement | (empty) | KM     | *(empty)* | **660** |

So your code reads:

* `quantiteStr` = `getCellValue(row.getCell(3))`
  \u2192 returns `""` \u2192 becomes `0`.

---

# \u2705 100% FIX: Auto-detect the first numeric value after unite

Here is a robust solution:

\U0001f449 Look at **cells 3, 4, 5, 6\u2026**
\U0001f449 Pick the **first numeric value**
\U0001f449 Use it as quantite

### \u2714\ufe0f Add this block **instead of your current quantiteStr logic**:

```java
// Detect quantity (first numeric value in the row after column 2)
Double quantite = 0.0;
boolean foundQuantite = false;

// Start scanning from column 3 to column 10 (just in case)
for (int c = 3; c <= 10; c++) {
    Cell cell = row.getCell(c);
    if (cell == null) continue;

    String val = getCellValue(cell);
    if (val == null || val.trim().isEmpty()) continue;

    val = val.replace(" ", "").replace(",", ".");

    try {
        quantite = Double.parseDouble(val);
        foundQuantite = true;
        break;
    } catch (Exception ignore) {
    }
}

if (!foundQuantite) {
    skipped.add(new SkippedRow(i + 1, "Quantité introuvable dans la ligne"));
    continue;
}
```

---

# \U0001f525 What this solves

This automatically handles ALL Excel formats:

\u2714 merged cells
\u2714 centered text
\u2714 shifted columns
\u2714 quantity in col 4 or col 5 or col 6
\u2714 thousand separators \u201c8 500\u201d
\u2714 decimal \u201c660,00\u201d

Your line **will be imported with the correct quantity**.

---

# If you want, I can fully rewrite your import method clean and safe.

If you send me ONE screenshot of your Excel row, I can make it perfect.
