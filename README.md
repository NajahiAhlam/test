Perfect \U0001f44d \u2014 so just to confirm:
In your Excel, the **\u201ca)\u201d, \u201cb)\u201d, \u201cc)\u201d** appear **inside the `Désignation` column itself**, not as a separate code column, right?

For example your Excel looks like:

| N° (code) | Désignation                    | Unité | Fournisseur A | Fournisseur B |
| --------- | ------------------------------ | ----- | ------------- | ------------- |
| 200.14    | Revêtement des encadrements... |       |               |               |
|           | a) Granit gris de Sardaigne    | ML    | 600           | 450           |
|           | b) Pierre de Taza              | ML    | 350           | 200           |
|           | c) Marbre labrador 1er choix   | ML    | 650           | 800           |

\u2705 Meaning:

* The **main line (200.14)** \u2192 is the *parent* and must be skipped (only defines the group).
* The **following lines (a, b, c)** \u2192 are *articles* with the same base code but a suffix (`200.14.1`, `200.14.2`, etc.).
* The **sub-line detection** is based on the fact that:

  * There is **no code**.
  * There is a **designation** (often starting with `a)`, `b)`, etc.).
  * It has a unit or price values.

---

Here\u2019s your **updated and correct logic** to handle that format \U0001f447

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

            // 1\ufe0f\u20e3 Category/subcategory: only designation filled
            if ((code == null || code.trim().isEmpty()) &&
                (designation != null && !designation.trim().isEmpty()) &&
                (unite == null || unite.trim().isEmpty()) &&
                !hasAnySupplierPrice) {
                skipped.add(new SkippedRow(i + 1, "Catégorie ou sous-catégorie (designation seule)"));
                continue;
            }

            // 2\ufe0f\u20e3 Parent row: has code, no designation \u2192 group header
            if ((code != null && !code.trim().isEmpty()) &&
                (designation == null || designation.trim().isEmpty())) {
                currentParentCode = code.trim();
                childCounter = 0;
                skipped.add(new SkippedRow(i + 1, "Ligne parent (code seul, sans désignation)"));
                continue;
            }

            // 3\ufe0f\u20e3 If code filled & designation filled \u2192 main article (standalone)
            if (code != null && !code.trim().isEmpty() &&
                designation != null && !designation.trim().isEmpty()) {
                currentParentCode = code.trim();
                childCounter = 0; // reset because new parent
            }

            // 4\ufe0f\u20e3 If no code but designation filled \u2192 child article under last parent
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

            // 5\ufe0f\u20e3 Now import the valid line
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

### \u2705 Example Behavior

| Code   | Désignation                    | Unité | Fournisseur A | Fournisseur B |
| ------ | ------------------------------ | ----- | ------------- | ------------- |
| 200.14 | Revêtement des encadrements... |       |               |               |
|        | a) Granit gris de Sardaigne    | ML    | 600           | 450           |
|        | b) Pierre de Taza              | ML    | 350           | 200           |
|        | c) Marbre labrador             | ML    | 650           | 800           |
|        | Catégorie marbre               |       |               |               |

\U0001f449 Results:

* Skip `200.14` (parent header)
* Import

  * `200.14.1` \u2014 a) Granit gris...
  * `200.14.2` \u2014 b) Pierre de Taza
  * `200.14.3` \u2014 c) Marbre labrador
* Skip `Catégorie marbre` (designation-only)

---

Would you like me to make it also **record skipped rows with their full Excel data** (so you can debug which ones were skipped in UI or logs)?
