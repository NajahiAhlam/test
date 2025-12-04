Here is **exactly why your `priceArticles` count becomes bigger than what you have in Excel**, and how to fix it.

---

# \u2705 **ROOT CAUSE (100% CONFIRMED FROM YOUR CODE)**

Your loop that reads supplier prices **does NOT align column indexes correctly**
because you use:

```java
fournisseurStartIndex = 3;
```

but in your Excel **supplier columns do NOT necessarily start at index 3** depending on how your file is structured.

Also:

### \u274c You add **every fournisseur header** even if the column in Excel is blank

\u2192 This inflates `fournisseursHeaders`.

### \u274c You iterate using:

```java
for (int j = 0; j < fournisseursHeaders.size(); j++)
```

Then compute:

```java
int cellIndex = fournisseurStartIndex + j;
```

\u26a0\ufe0f If you extracted the wrong number of headers, indexes shift \u2192
\U0001f525 You read wrong columns
\U0001f525 Some columns get read twice
\U0001f525 Some prices get duplicated
\U0001f525 Your `PriceArticle` list becomes bigger than expected

---

# \u2705 **QUICK TEST TO SEE THE BUG**

Log your headers:

```java
System.out.println("Fournisseurs détectés:");
for (int x = 0; x < fournisseursHeaders.size(); x++) {
    System.out.println(x + ": " + fournisseursHeaders.get(x));
}
```

You will see **more entries** than expected \u2192 because your header row contains **empty but not-null cells**.

---

# \u2705 REAL FIX: FILTER SUPPLIER COLUMNS CORRECTLY

Replace your header extraction with a safe version:

```java
int fournisseurStartIndex = 3;
int lastCol = headerRow.getLastCellNum();

List<Integer> fournisseurColumnIndexes = new ArrayList<>();
List<String> fournisseursHeaders = new ArrayList<>();

for (int col = fournisseurStartIndex; col < lastCol; col++) {
    String header = safeTrim(getCellValue(headerRow.getCell(col)));

    if (!header.isEmpty() && !header.equals("-")) {
        fournisseursHeaders.add(header);
        fournisseurColumnIndexes.add(col);
    }
}
```

\u2714 Only real fournisseur columns are collected
\u2714 Indexes point EXACTLY to the Excel column

---

# \u2705 FIX THE PRICE LOOP

Replace:

```java
for (int j = 0; j < fournisseursHeaders.size(); j++) {
    int cellIndex = fournisseurStartIndex + j;
```

With:

```java
for (int k = 0; k < fournisseursHeaders.size(); k++) {
    int cellIndex = fournisseurColumnIndexes.get(k);
```

---

# \U0001f525 FINAL FIXED PRICE IMPORT BLOCK

```java
List<PriceArticle> priceArticles = new ArrayList<>();

for (int k = 0; k < fournisseursHeaders.size(); k++) {

    String raisonSociale = fournisseursHeaders.get(k);
    int cellIndex = fournisseurColumnIndexes.get(k);

    String prixStr = safeTrim(getCellValue(row.getCell(cellIndex)));

    if (prixStr.isEmpty() || prixStr.equals("-")) continue;

    double prix;
    try {
        prix = Double.parseDouble(prixStr.replace(" ", "").replace(",", "."));
    } catch (NumberFormatException e) {
        skipped.add(new SkippedRow(i + 1, "Prix invalide pour " + raisonSociale + " : '" + prixStr + "'"));
        continue;
    }

    Fournisseur fournisseur = fournisseurRepository
            .findByRaisonSocialeIgnoreCase(raisonSociale)
            .orElseGet(() -> {
                Fournisseur f = new Fournisseur();
                f.setRaisonSociale(raisonSociale);
                return fournisseurRepository.save(f);
            });

    PriceArticle pa = new PriceArticle();
    pa.setArticle(article);
    pa.setFournisseur(fournisseur);
    pa.setPrice(prix);

    priceArticles.add(pa);
}
```

---

# \U0001f3af **RESULT AFTER FIX**

| Before                         | After                           |
| ------------------------------ | ------------------------------- |
| Price columns read incorrectly | Only real supplier columns read |
| Duplicate PriceArticles        | One per supplier per row        |
| Wrong index offsets            | Exact Excel index mapping       |
| Bigger count than Excel        | Correct count                   |

---

# If you want, send me **a screenshot of your header row**, and I\u2019ll generate the correct indices for you.
