Excellent question \U0001f44f \u2014 this is actually a **very common problem** when parsing Excel files:
some rows get skipped silently, and you don\u2019t know **why** (e.g., maybe a header mismatch, empty quantity cell, parse error, etc.).

Let\u2019s fix that properly \u2014 without breaking your existing import logic.

---

## \u2705 Here\u2019s what we\u2019ll do

We\u2019ll **log (and collect)** every skipped line along with the **reason** it was skipped, so you can:

* See which rows were ignored
* Know *why* each one was skipped (missing quantity, invalid number, missing code, etc.)
* Optionally, send this info back to the front end as part of your response

---

## \U0001f9e9 Updated `excelToArticles` \u2014 with skipped row tracking

```java
public class ExcelArticleReader {

    public static class SkippedRow {
        private final int rowIndex;
        private final String reason;

        public SkippedRow(int rowIndex, String reason) {
            this.rowIndex = rowIndex;
            this.reason = reason;
        }

        public int getRowIndex() { return rowIndex; }
        public String getReason() { return reason; }

        @Override
        public String toString() {
            return "Row " + rowIndex + " skipped: " + reason;
        }
    }

    public static class ExcelImportResult {
        private final List<Article> articles;
        private final List<SkippedRow> skippedRows;

        public ExcelImportResult(List<Article> articles, List<SkippedRow> skippedRows) {
            this.articles = articles;
            this.skippedRows = skippedRows;
        }

        public List<Article> getArticles() { return articles; }
        public List<SkippedRow> getSkippedRows() { return skippedRows; }
    }

    // \u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500
    // MAIN METHOD
    // \u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500\u2500
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

            for (int i = 1; i <= sheet.getLastRowNum(); i++) {
                Row row = sheet.getRow(i);
                if (row == null || isRowEmpty(row)) {
                    skipped.add(new SkippedRow(i + 1, "Ligne vide"));
                    continue;
                }

                String quantiteStr = getCellValue(row.getCell(COL_QUANTITE));
                if (quantiteStr == null || quantiteStr.trim().isEmpty()) {
                    skipped.add(new SkippedRow(i + 1, "Quantité vide (catégorie ou sous-catégorie)"));
                    continue;
                }

                try {
                    Article article = new Article();
                    article.setCode(getCellValue(row.getCell(COL_CODE)));
                    article.setDesignation(getCellValue(row.getCell(COL_DESIGNATION)));
                    article.setUnite(getCellValue(row.getCell(COL_UNITE)));
                    article.setQuantite(quantiteStr);
                    article.setTypeArticle(getCellValue(row.getCell(COL_TYPE_ARTICLE)));
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
}
```

---

## \u2705 Example of usage in your service

```java
ExcelArticleReader.ExcelImportResult result = excelArticleReader.excelToArticles(file.getInputStream(), projetId);
List<Article> articles = result.getArticles();
List<ExcelArticleReader.SkippedRow> skippedRows = result.getSkippedRows();

articleRepository.saveAll(articles);

// Optional: log skipped rows
skippedRows.forEach(s -> log.warn(s.toString()));

// Optionally return them in your API response
if (!skippedRows.isEmpty()) {
    log.info("\u26a0\ufe0f " + skippedRows.size() + " lignes ignorées lors de l'import.");
}
```

---

## \u2705 What you get

* You\u2019ll **see every skipped line and the reason** (e.g. *\u201cRow 8 skipped: Quantité vide\u201d*).
* You can return the skipped lines to the front-end if you want to show a message like:

  > \u201c3 lignes ont été ignorées car la quantité est vide.\u201d
* No changes needed in your Excel format \u2014 it still supports supplier columns and all your logic.

---

Would you like me to show how to **return the skipped lines** to the frontend (as part of your API response) so the UI can display them after upload?
