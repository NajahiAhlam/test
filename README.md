Good catch \u2014 you were right that the *only-designation* case must be explicitly detected and skipped. I updated your method so it checks **if designation is the only non-empty cell** (i.e. `code`, `unite`, `typeArticle` and **all supplier price columns** are empty) and skips that row.

Below is the full `excelToArticles` method with that explicit check added (I placed the check early, before parent/child logic). I kept the rest of your logic intact.

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

            String code = safeTrim(getCellValue(row.getCell(COL_CODE)));
            String designation = safeTrim(getCellValue(row.getCell(COL_DESIGNATION)));
            String unite = safeTrim(getCellValue(row.getCell(COL_UNITE)));
            String typeArticle = safeTrim(getCellValue(row.getCell(COL_TYPE_ARTICLE)));

            // Check supplier price cells
            boolean hasAnySupplierPrice = false;
            for (int j = fournisseurStartIndex; j < headerRow.getLastCellNum(); j++) {
                String priceCell = safeTrim(getCellValue(row.getCell(j)));
                if (!priceCell.isEmpty()) {
                    hasAnySupplierPrice = true;
                    break;
                }
            }

            // ==== NEW: Skip rows where ONLY designation is filled and everything else is empty ====
            boolean hasCode = !code.isEmpty();
            boolean hasDesignation = !designation.isEmpty();
            boolean hasUnit = !unite.isEmpty();
            boolean hasType = !typeArticle.isEmpty();

            if (hasDesignation && !hasCode && !hasUnit && !hasType && !hasAnySupplierPrice) {
                skipped.add(new SkippedRow(i + 1, "Catégorie / sous-catégorie (seulement désignation remplie)"));
                continue;
            }
            // ===========================================================================

            // Existing logic: parent row (code only, no designation) => set currentParentCode and skip
            if (hasCode && !hasDesignation && !hasUnit && !hasAnySupplierPrice && typeArticle.isEmpty()) {
                currentParentCode = code.trim();
                childCounter = 0;
                skipped.add(new SkippedRow(i + 1, "Ligne parent (code seul, sans désignation)"));
                continue;
            }

            // If both code and designation are present, treat as a main article and reset parent
            if (hasCode && hasDesignation) {
                currentParentCode = code.trim();
                childCounter = 0;
            }

            // If no code but designation present => child line under last parent -> assign suffix
            if (!hasCode && hasDesignation) {
                if (currentParentCode != null) {
                    childCounter++;
                    code = currentParentCode + "." + childCounter;
                } else {
                    skipped.add(new SkippedRow(i + 1, "Sous-ligne sans code parent détecté"));
                    continue;
                }
            }

            // Now build Article and its PriceArticle list
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
                    String prixStr = safeTrim(getCellValue(row.getCell(cellIndex)));

                    if (prixStr.isEmpty()) continue;

                    double prix;
                    try {
                        prix = Double.parseDouble(prixStr.replace(",", "."));
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

/* Helper to avoid NPE and trim */
private String safeTrim(String s) {
    return s == null ? "" : s.trim();
}
```

### Notes & small improvements included

* `safeTrim(...)` used to avoid NPEs and consistently treat blank cells as empty strings.
* The new `ONLY designation` check is done before parent/child code handling so category lines are skipped early.
* Price parsing tolerates comma decimal separators by `replace(",", ".")` before `Double.parseDouble`.
* Skipped rows contain the row number and reason so you can debug or return that to the front-end.

---

If you want, I can:

* Add the skipped rows into the service response (so your UI shows them),
* Or write a small test data Excel and show which rows would be skipped vs imported. Which one do you prefer?
