Excellent \u2014 that\u2019s a good example, and I understand exactly what\u2019s happening in your Excel now.
You have **\u201cparent\u201d rows** with a code (like `200.14`) and **\u201cchild\u201d rows** (a, b, c, d\u2026) that belong to that parent.

Here\u2019s how the logic should work:

---

### \u2705 **New Rules Recap**

1. **Skip row** if:

   * Only `designation` is filled (all other columns, including code, are empty).
     \u2192 Means it\u2019s a *category/subcategory title*, not an article.

2. **Skip row** if:

   * `code` is filled, but `designation` and others are empty.
     \u2192 Means it\u2019s a *group header*; the following rows depend on it.

3. **For child rows:**

   * When a row has no code, **use the last valid code** and append `.1`, `.2`, `.3`, etc.
   * Example: if last code = `200.14`
     \u2192 next rows without code become: `200.14.1`, `200.14.2`, etc.

4. **Import only rows that have a `designation` filled and not skipped.**

---

### \U0001f9e0 **Implementation Plan**

We\u2019ll:

* Track the **current parent code**.
* Track the **suffix counter** for children.
* Decide skip reasons for `skippedRows` list.

---

### \U0001f9e9 **Updated Code**

Here\u2019s your updated `excelToArticles` method:

```java
public ExcelImportResult excelToArticles(InputStream is, Long projetId) {
    List<Article> articles = new ArrayList<>();
    List<SkippedRow> skipped = new ArrayList<>();

    try (Workbook workbook = new XSSFWorkbook(is)) {
        Sheet sheet = workbook.getSheet(SHEET_NAME);
        if (sheet == null) throw new RuntimeException("Feuille '" + SHEET_NAME + "' introuvable");

        Projet projet = projetRepository.findById(projetId)
                .orElseThrow(() -> new RuntimeException("Projet introuvable ID: " + projetId));

        Row headerRow = sheet.getRow(0);
        if (headerRow == null) throw new RuntimeException("En-tête manquant");
        validateHeaders(headerRow);

        int fournisseurStartIndex = 5;
        List<String> fournisseursHeaders = new ArrayList<>();
        for (int i = fournisseurStartIndex; i < headerRow.getLastCellNum(); i++) {
            String raisonSociale = normalizeHeader(getCellValue(headerRow.getCell(i)));
            if (!raisonSociale.isEmpty()) fournisseursHeaders.add(raisonSociale);
        }

        String currentParentCode = null;
        int childCounter = 0;

        for (int i = 1; i <= sheet.getLastRowNum(); i++) {
            Row row = sheet.getRow(i);
            if (row == null || isRowEmpty(row)) {
                skipped.add(new SkippedRow(i + 1, "Ligne vide"));
                continue;
            }

            String code = getCellValue(row.getCell(COL_CODE));
            String designation = getCellValue(row.getCell(COL_DESIGNATION));
            String unite = getCellValue(row.getCell(COL_UNITE));
            String typeArticle = getCellValue(row.getCell(COL_TYPE_ARTICLE));

            boolean hasAnySupplierPrice = false;
            for (int j = fournisseurStartIndex; j < headerRow.getLastCellNum(); j++) {
                if (!getCellValue(row.getCell(j)).trim().isEmpty()) {
                    hasAnySupplierPrice = true;
                    break;
                }
            }

            // 1\ufe0f\u20e3 Case: only designation filled \u2192 skip (category/sub-category)
            if ((code == null || code.trim().isEmpty()) &&
                (designation != null && !designation.trim().isEmpty()) &&
                (unite == null || unite.trim().isEmpty()) &&
                !hasAnySupplierPrice) {
                skipped.add(new SkippedRow(i + 1, "Catégorie ou sous-catégorie (designation seule)"));
                continue;
            }

            // 2\ufe0f\u20e3 Case: code filled, but designation empty \u2192 group header
            if ((code != null && !code.trim().isEmpty()) &&
                (designation == null || designation.trim().isEmpty())) {
                currentParentCode = code.trim();
                childCounter = 0;
                skipped.add(new SkippedRow(i + 1, "Ligne parent (code seul, sans désignation)"));
                continue;
            }

            // 3\ufe0f\u20e3 Case: child row (no code, designation filled)
            if ((code == null || code.trim().isEmpty()) &&
                (designation != null && !designation.trim().isEmpty())) {

                if (currentParentCode != null) {
                    childCounter++;
                    code = currentParentCode + "." + childCounter;
                } else {
                    skipped.add(new SkippedRow(i + 1, "Sous-ligne sans code parent détecté"));
                    continue;
                }
            }

            // 4\ufe0f\u20e3 Create and save article
            try {
                Article article = new Article();
                article.setCode(code);
                article.setDesignation(designation);
                article.setUnite(unite);
                article.setTypeArticle(typeArticle);
                article.setProjet(projet);

                List<PriceArticle> priceArticles = new ArrayList<>();
                for (int j = 0; j < fournisseursHeaders.size(); j++) {
                    String raisonSociale = fournisseursHeaders.get(j);
                    int cellIndex = fournisseurStartIndex + j;
                    String prixStr = getCellValue(row.getCell(cellIndex));

                    if (prixStr == null || prixStr.trim().isEmpty()) continue;

                    double prix;
                    try {
                        prix = Double.parseDouble(prixStr);
                    } catch (NumberFormatException e) {
                        skipped.add(new SkippedRow(i + 1, "Prix invalide pour " + raisonSociale));
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

                article.setPriceArticles(priceArticles);
                articles.add(article);

            } catch (Exception e) {
                skipped.add(new SkippedRow(i + 1, "Erreur inattendue: " + e.getMessage()));
            }
        }

    } catch (IOException e) {
        throw new RuntimeException("Erreur de lecture du fichier: " + e.getMessage(), e);
    }

    return new ExcelImportResult(articles, skipped);
}
```

---

### \U0001f9fe **Result Example**

If your Excel looks like this:

| Code   | Désignation    | Unite | Fournisseur A | Fournisseur B |
| ------ | -------------- | ----- | ------------- | ------------- |
| 200.14 | Revêtement...  |       |               |               |
| a)     | Granit gris... | ML    | 600           | 450           |
| b)     | Pierre de Taza | ML    | 350           | 200           |
|        | Catégorie XYZ  |       |               |               |

Then:

* `200.14` \u2192 skipped as parent
* `a)` \u2192 imported as `200.14.1`
* `b)` \u2192 imported as `200.14.2`
* `Catégorie XYZ` \u2192 skipped as \u201ccategory title\u201d

---

Would you like me to also **log skipped lines to a separate Excel file** (for user feedback after import)?
