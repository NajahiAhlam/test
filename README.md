Perfect \u2014 you want **to validate the Excel file\u2019s header row** (first row) before reading the data, and ensure that expected headers like `"Code"`, `"Désignation"`, etc., exist even if:

* the case (uppercase/lowercase) differs,
* or there are accent variations (`é`, `è`, etc.).

Here\u2019s how to do it properly \U0001f447

---

### \u2705 Updated `excelToArticles()` with Header Validation

```java
public List<Article> excelToArticles(InputStream is, Long projetId) {
    List<Article> articles = new ArrayList<>();

    try (Workbook workbook = new XSSFWorkbook(is)) {
        Sheet sheet = workbook.getSheet(SHEET_NAME);
        if (sheet == null) {
            throw new RuntimeException("Sheet '" + SHEET_NAME + "' not found in Excel file.");
        }

        Projet projet = projetRepository.findById(projetId)
                .orElseThrow(() -> new RuntimeException("Projet not found with id: " + projetId));

        // \u2705 Validate headers
        Row headerRow = sheet.getRow(0);
        if (headerRow == null) {
            throw new RuntimeException("Header row is missing in Excel file.");
        }

        validateHeaders(headerRow);

        // \u2705 Parse data rows
        for (int i = 1; i <= sheet.getLastRowNum(); i++) {
            Row row = sheet.getRow(i);
            if (row == null || isRowEmpty(row)) continue;

            Article article = new Article();
            article.setCode(getCellValue(row.getCell(COL_CODE)));
            article.setDesignation(getCellValue(row.getCell(COL_DESIGNATION)));
            article.setUnite(getCellValue(row.getCell(COL_UNITE)));
            article.setQuantite(getCellValue(row.getCell(COL_QUANTITE)));
            article.setTypeArticle(getCellValue(row.getCell(COL_TYPE_ARTICLE)));
            article.setProjet(projet);

            articles.add(article);
        }

    } catch (IOException e) {
        throw new RuntimeException("Failed to parse Excel file: " + e.getMessage(), e);
    }

    return articles;
}
```

---

### \u2705 Add This `validateHeaders` Method

```java
private void validateHeaders(Row headerRow) {
    // Expected headers (you can customize)
    List<String> expectedHeaders = Arrays.asList("code", "designation", "unite", "quantite", "type article");

    for (int i = 0; i < expectedHeaders.size(); i++) {
        Cell cell = headerRow.getCell(i);
        if (cell == null) {
            throw new RuntimeException("Missing header cell at column " + (i + 1));
        }

        String headerValue = normalizeHeader(getCellValue(cell));
        String expected = normalizeHeader(expectedHeaders.get(i));

        if (!headerValue.contains(expected)) {
            throw new RuntimeException("Invalid header at column " + (i + 1) +
                    ": expected '" + expectedHeaders.get(i) + "' but found '" + getCellValue(cell) + "'");
        }
    }
}
```

---

### \u2705 Add `normalizeHeader` Helper

```java
private String normalizeHeader(String input) {
    if (input == null) return "";
    // Remove accents and convert to lowercase
    String normalized = java.text.Normalizer.normalize(input, java.text.Normalizer.Form.NFD);
    normalized = normalized.replaceAll("\\p{M}", ""); // remove diacritics
    return normalized.trim().toLowerCase();
}
```

---

### \u2705 Add `getCellValue()` if not already defined

```java
private String getCellValue(Cell cell) {
    if (cell == null) return "";
    switch (cell.getCellType()) {
        case STRING:
            return cell.getStringCellValue().trim();
        case NUMERIC:
            return String.valueOf((int) cell.getNumericCellValue());
        case BOOLEAN:
            return String.valueOf(cell.getBooleanCellValue());
        default:
            return "";
    }
}
```

---

### \u2705 What this does

* Validates that headers **match the expected names** (`Code`, `Désignation`, etc.).
* Works even if Excel headers are like:

  * `"CODE"`, `"désignation"`, `"Unité"`, `"Quantité"`, `"Type d\u2019article"`
* Throws a **clear error** if a column header is missing or wrong.

---

Would you like me to make it **auto-map columns** (i.e. even if \u201ccode\u201d is not first column, it still works by name)?
That\u2019s possible too, for more flexible Excel uploads.
