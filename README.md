Perfect! You already have the service skeleton with helpers like `getCellValue`, `isRowEmpty`, and header validation. Now we can implement the **main import method for Lot Electricité** that will parse the Excel, handle the `Marque`/`Reference` logic, and return `ExcelImportResult`.

Here\u2019s a complete method you can add inside your `ExcelArticleLotElectriciteReader`:

```java
public ExcelImportResult importExcel(MultipartFile file, List<Fournisseur> fournisseurs) {
    List<ArticleLotElectricite> articles = new ArrayList<>();
    List<SkippedRow> skippedRows = new ArrayList<>();

    try (Workbook workbook = new XSSFWorkbook(file.getInputStream())) {
        Sheet sheet = workbook.getSheet(SHEET_NAME);
        if (sheet == null) {
            throw new RuntimeException("Sheet '" + SHEET_NAME + "' not found in Excel file");
        }

        // Header row (assume first row)
        Row headerRow = sheet.getRow(0);
        validateHeaders(headerRow);

        // Map column index to fournisseur
        Map<Integer, Fournisseur> fournisseurColumns = new HashMap<>();
        for (int c = COL_UNITE + 1; c < headerRow.getLastCellNum(); c++) {
            String fournisseurName = safeTrim(getCellValue(headerRow.getCell(c)));
            fournisseurs.stream()
                    .filter(f -> f.getRaisonSociale().equalsIgnoreCase(fournisseurName))
                    .findFirst()
                    .ifPresent(f -> fournisseurColumns.put(c, f));
        }

        ArticleLotElectricite currentArticle = null;
        String currentCode = "";
        String currentDesignation = "";
        String currentUnite = "";

        for (int r = 1; r <= sheet.getLastRowNum(); r++) {
            Row row = sheet.getRow(r);
            if (isRowEmpty(row)) continue;

            String code = safeTrim(getCellValue(row.getCell(COL_CODE)));
            String designation = safeTrim(getCellValue(row.getCell(COL_DESIGNATION)));
            String unite = safeTrim(getCellValue(row.getCell(COL_UNITE)));

            // Detect new article row (has code + designation)
            boolean isNewArticle = !code.isEmpty() && !designation.isEmpty();

            if (isNewArticle) {
                currentCode = code;
                currentDesignation = designation;
                currentUnite = unite;

                currentArticle = new ArticleLotElectricite();
                currentArticle.setCode(currentCode);
                currentArticle.setDesignation(currentDesignation);
                currentArticle.setUnite(currentUnite);
                currentArticle.setPriceArticles(new ArrayList<>());

                articles.add(currentArticle);
            }

            if (currentArticle == null) {
                skippedRows.add(new SkippedRow(r, "No current article to assign prices"));
                continue;
            }

            // Marque & Reference
            String marque = safeTrim(getCellValue(row.getCell(COL_DESIGNATION + 1))); // next column after designation
            String reference = safeTrim(getCellValue(row.getCell(COL_DESIGNATION + 2))); // next column after marque

            // Process prices for fournisseurs
            for (Map.Entry<Integer, Fournisseur> entry : fournisseurColumns.entrySet()) {
                int colIndex = entry.getKey();
                Fournisseur fournisseur = entry.getValue();

                String priceStr = safeTrim(getCellValue(row.getCell(colIndex)));
                if (priceStr.isEmpty()) continue;

                try {
                    double price = Double.parseDouble(priceStr.replaceAll("[\\s,]", ""));
                    PriceArticleLotElectricite priceArticle = new PriceArticleLotElectricite();
                    priceArticle.setArticleLotElectricite(currentArticle);
                    priceArticle.setFournisseur(fournisseur);
                    priceArticle.setPrice(price);

                    // Only set marque/reference if exists
                    if (!marque.isEmpty()) priceArticle.setMarque(marque);
                    if (!reference.isEmpty()) priceArticle.setReference(reference);

                    currentArticle.getPriceArticles().add(priceArticle);

                } catch (NumberFormatException ex) {
                    skippedRows.add(new SkippedRow(r, "Invalid price format for fournisseur " + fournisseur.getRaisonSociale() + ": " + priceStr));
                }
            }
        }

    } catch (Exception e) {
        log.error("Failed to import Excel for Lot Electricité", e);
        throw new RuntimeException("Failed to import Excel: " + e.getMessage(), e);
    }

    return new ExcelImportResult(articles, skippedRows);
}
```

---

### \u2705 How it Works

1. **Detects new articles** by checking if `code` and `designation` are present.
2. **Keeps track of the current article** so that rows with only Marque/Reference still get assigned to it.
3. **Maps columns to fournisseurs** dynamically from the header row.
4. **Parses prices**, removing spaces/commas, and creates `PriceArticleLotElectricite` objects.
5. **Handles Marque/Reference** for Electricité articles.
6. **Returns skipped rows** if any parsing error occurs.

---

If you want, I can also **improve this to merge rows where multiple Marque/Reference share the same price** \u2014 so your Excel formatting with merged cells will be correctly handled automatically. This is common in Electricité lots.

Do you want me to do that next?
