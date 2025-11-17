To implement a similar method for your `ArticleHorsBordereau` entity, which reads from an Excel file and processes the quantity and unit price, here's how you can structure it.

### 1. **ArticleHorsBordereau Entity**:

The `ArticleHorsBordereau` class is already provided. You can use the following class definition, where the `totalMontant` will be calculated as `quantite * prixUnitaire` for each row.

```java
@Entity
public class ArticleHorsBordereau {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String code;
    private String designation;
    private String unite;
    private Double prixUnitaire = 0.0;
    private Double montant = 0.0;     // Quantity * Unit Price
    private Double totalMontant = 0.0;  // Will be calculated for each line
    @ManyToOne
    @JoinColumn(name = "projet_id", nullable = false)
    private Projet projet;

    // Getters and Setters
}
```

### 2. **Import Method for ArticleHorsBordereau**:

Here\u2019s how you could implement the method to import the "articles hors bordereau" file into the database.

```java
public ImportResult importArticleHorsBordereauExcel(InputStream is, Long projetId) {
    List<ArticleHorsBordereau> imported = new ArrayList<>();
    List<SkippedRow> skipped = new ArrayList<>();

    try (Workbook workbook = new XSSFWorkbook(is)) {
        Sheet sheet = workbook.getSheetAt(0);
        if (sheet == null) throw new RuntimeException("Aucune feuille trouvée dans le fichier Excel");

        // Retrieve the Project based on the projetId
        Projet projet = projetRepository.findById(projetId)
                .orElseThrow(() -> new RuntimeException("Projet introuvable avec ID: " + projetId));

        // Iterate through each row starting from the second row (row 1)
        for (int i = 1; i <= sheet.getLastRowNum(); i++) {
            Row row = sheet.getRow(i);
            if (row == null || isRowEmpty(row)) {
                skipped.add(new SkippedRow(i + 1, "Ligne vide"));
                continue;
            }

            try {
                // Read each cell in the row
                String code = getCellValue(row.getCell(0)); // N°
                String designation = getCellValue(row.getCell(1)); // Designation
                String unite = getCellValue(row.getCell(2)); // Unite
                String quantiteStr = getCellValue(row.getCell(3)); // Quantité
                String prixUnitaireStr = getCellValue(row.getCell(4)); // Prix Unitaire

                // Validate input data
                if ((code == null || code.isEmpty()) || (designation == null || designation.isEmpty())) {
                    skipped.add(new SkippedRow(i + 1, "Code et désignation manquants"));
                    continue;
                }

                // Parse Quantité
                Double quantite = 0.0;
                if (quantiteStr != null && !quantiteStr.trim().isEmpty()) {
                    try {
                        quantite = Double.parseDouble(quantiteStr.replace(",", "."));
                    } catch (NumberFormatException e) {
                        skipped.add(new SkippedRow(i + 1, "Quantité invalide: '" + quantiteStr + "'"));
                        continue;
                    }
                }

                // Parse Prix Unitaire
                Double prixUnitaire = 0.0;
                if (prixUnitaireStr != null && !prixUnitaireStr.trim().isEmpty()) {
                    try {
                        prixUnitaire = Double.parseDouble(prixUnitaireStr.replace(",", "."));
                    } catch (NumberFormatException e) {
                        skipped.add(new SkippedRow(i + 1, "Prix unitaire invalide: '" + prixUnitaireStr + "'"));
                        continue;
                    }
                }

                // Calculate Montant (Total)
                Double montant = quantite * prixUnitaire;

                // Look for an existing Article based on code or designation (or handle as needed)
                Optional<Article> optionalArticle = articleRepository.findByCode(code);
                if (!optionalArticle.isPresent()) {
                    skipped.add(new SkippedRow(i + 1, "Article introuvable pour le code: " + code));
                    continue;
                }

                Article article = optionalArticle.get();

                // Create and save ArticleHorsBordereau
                ArticleHorsBordereau articleHorsBordereau = new ArticleHorsBordereau();
                articleHorsBordereau.setCode(code);
                articleHorsBordereau.setDesignation(designation);
                articleHorsBordereau.setUnite(unite);
                articleHorsBordereau.setPrixUnitaire(prixUnitaire);
                articleHorsBordereau.setMontant(montant);
                articleHorsBordereau.setProjet(projet);
                articleHorsBordereau.setTotalMontant(montant); // Set the total amount (same as montant in this case)

                // Save to the repository
                ArticleHorsBordereau saved = articleHorsBordereauRepository.save(articleHorsBordereau);
                imported.add(saved);

            } catch (Exception e) {
                skipped.add(new SkippedRow(i + 1, "Erreur inattendue: " + e.getMessage()));
            }
        }

    } catch (IOException e) {
        throw new RuntimeException("Erreur lors de la lecture du fichier Excel", e);
    }

    // Return the import result with imported and skipped rows
    return new ImportResult(imported, skipped);
}
```

### 3. **Explanation of the Code**:

* **Parsing the Excel Rows**: The method reads the rows from the Excel file (starting from the second row to skip the header) and extracts the values for each article (code, designation, unit, quantity, unit price).

* **Data Validation**: For each row, the code checks if `code` and `designation` are present. If not, the row is skipped. It also checks that the quantity and unit price are valid numbers.

* **Look Up Article**: The code looks up the article in the `Article` table based on the article code (you can modify this to use the `designation` if you prefer, or even both).

* **Montant Calculation**: The `montant` for each row is calculated as `quantite * prixUnitaire`, and the total amount is stored in `totalMontant`.

* **Save the Data**: After processing the row, it creates an instance of `ArticleHorsBordereau` and saves it to the database.

### 4. **Helper Methods**:

You may have already defined these methods in your code to safely retrieve cell values and check row emptiness.

```java
private String getCellValue(Cell cell) {
    return cell == null ? "" : cell.toString().trim();
}

private boolean isRowEmpty(Row row) {
    return row == null || row.getPhysicalNumberOfCells() == 0;
}
```

### 5. **ImportResult Class**:

This class is used to return the results of the import, indicating which rows were successfully imported and which were skipped.

```java
public class ImportResult {
    private List<ArticleHorsBordereau> imported;
    private List<SkippedRow> skipped;

    // Constructor, getters, and setters
}
```

### 6. **SkippedRow Class**:

This is a utility class to keep track of rows that were skipped due to errors.

```java
public class SkippedRow {
    private int rowNum;
    private String reason;

    // Constructor, getters, and setters
}
```

### Conclusion:

This method processes the Excel file for "articles hors bordereau", extracts the necessary data (article code, designation, unit, quantity, unit price), calculates the total amount, and stores it in the `ArticleHorsBordereau` entity linked to a project. It handles errors, skips invalid rows, and returns the results for review.
