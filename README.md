Perfect \U0001f44c \u2014 you\u2019re now merging the two import logics into one method, where:

* You import both **articles** and their **fournisseurs + prices** in a single upload.
* You **skip category/subcategory rows** \u2192 those have a **null quantity cell** (not zero).
* You still validate headers (with case, accents, and "Type d'article" variations).

Here\u2019s the **clean, corrected, production-ready version** of your new logic \U0001f447

---

### \u2705 Updated `excelToArticles` method (unified logic)

```java
public List<Article> excelToArticles(InputStream is, Long projetId) {
    List<Article> articles = new ArrayList<>();

    try (Workbook workbook = new XSSFWorkbook(is)) {
        Sheet sheet = workbook.getSheet(SHEET_NAME);
        if (sheet == null) {
            throw new RuntimeException("La feuille '" + SHEET_NAME + "' est introuvable dans le fichier Excel.");
        }

        Projet projet = projetRepository.findById(projetId)
                .orElseThrow(() -> new RuntimeException("Projet introuvable avec l'ID : " + projetId));

        Row headerRow = sheet.getRow(0);
        if (headerRow == null) {
            throw new RuntimeException("La première ligne (en-têtes) est manquante.");
        }

        // \u2705 Validate core headers
        validateHeaders(headerRow);

        // \u2705 Detect fournisseur headers dynamically
        int fournisseurStartIndex = 5;
        List<String> fournisseursHeaders = new ArrayList<>();

        for (int i = fournisseurStartIndex; i < headerRow.getLastCellNum(); i++) {
            String raisonSociale = normalizeHeader(getCellValue(headerRow.getCell(i)));
            if (!raisonSociale.isEmpty()) {
                fournisseursHeaders.add(raisonSociale);
            }
        }

        // \u2705 Process data rows
        for (int i = 1; i <= sheet.getLastRowNum(); i++) {
            Row row = sheet.getRow(i);
            if (row == null || isRowEmpty(row)) continue;

            String quantiteStr = getCellValue(row.getCell(COL_QUANTITE));
            if (quantiteStr == null || quantiteStr.trim().isEmpty()) {
                // Skip category/subcategory (no quantity)
                continue;
            }

            Article article = new Article();
            article.setCode(getCellValue(row.getCell(COL_CODE)));
            article.setDesignation(getCellValue(row.getCell(COL_DESIGNATION)));
            article.setUnite(getCellValue(row.getCell(COL_UNITE)));
            article.setQuantite(quantiteStr);
            article.setTypeArticle(getCellValue(row.getCell(COL_TYPE_ARTICLE)));
            article.setProjet(projet);

            // \u2705 Handle fournisseurs and prices in the same row
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
        }

    } catch (IOException e) {
        throw new RuntimeException("Erreur de lecture du fichier Excel : " + e.getMessage(), e);
    }

    return articles;
}
```

---

### \u2705 Helper methods (include them if not already present)

```java
private boolean isRowEmpty(Row row) {
    if (row == null) return true;
    for (int c = row.getFirstCellNum(); c < row.getLastCellNum(); c++) {
        Cell cell = row.getCell(c);
        if (cell != null && cell.getCellType() != CellType.BLANK && !getCellValue(cell).trim().isEmpty()) {
            return false;
        }
    }
    return true;
}

private String getCellValue(Cell cell) {
    if (cell == null) return "";
    switch (cell.getCellType()) {
        case STRING: return cell.getStringCellValue().trim();
        case NUMERIC:
            if (DateUtil.isCellDateFormatted(cell))
                return cell.getDateCellValue().toString();
            else
                return String.valueOf((long) cell.getNumericCellValue());
        case BOOLEAN: return String.valueOf(cell.getBooleanCellValue());
        default: return "";
    }
}

private void validateHeaders(Row headerRow) {
    List<String> requiredHeaders = Arrays.asList("code", "désignation", "designation", "unité", "unite", "quantité", "quantite", "type article", "type d'article");

    for (int i = 0; i < 5; i++) {
        String normalized = normalizeHeader(getCellValue(headerRow.getCell(i)));
        boolean match = requiredHeaders.stream().anyMatch(h -> normalized.contains(h.replace("'", "")));

        if (!match) {
            throw new RuntimeException("Colonne invalide à la position " + (i + 1) + " : " + normalized);
        }
    }
}

private String normalizeHeader(String header) {
    if (header == null) return "";
    return Normalizer
            .normalize(header, Normalizer.Form.NFD)
            .replaceAll("\\p{M}", "")
            .toLowerCase()
            .replaceAll("[^a-z0-9 ]", "")
            .trim();
}
```

---

### \u2705 Summary of what this version does

| Feature                              | Description                                                                             |
| ------------------------------------ | --------------------------------------------------------------------------------------- |
| \U0001f9fe **Header validation**             | Ignores case, accents (`é`, `è`, etc.), and accepts \u201cType d\u2019article\u201d or \u201cType Article\u201d. |
| \U0001f4e6 **Single import**                 | Handles both articles and supplier prices in one pass.                                  |
| \U0001f6ab **Skip categories/subcategories** | Ignores rows with **null** (empty) quantity \u2014 but **keeps** rows with `0`.              |
| \U0001f501 **Auto-create suppliers**         | Creates `Fournisseur` if not found.                                                     |
| \U0001f4be **Links price + article**         | Associates `PriceArticle` with the current article and supplier.                        |

---

Would you like me to also include **saving logic** (persisting articles and their prices with repository saves) right after the loop?
That would make it ready for production (currently it just builds the list).
