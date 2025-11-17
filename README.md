For the new step where you have "articles hors bordereau", you need to create a structure that allows for reading this specific file format and storing the relevant details for each article along with its quantity, unit price, and calculated amount. Based on the information you've provided, here's how you can structure your classes and read the Excel file.

### 1. **Class Structure**

First, we will define an entity that represents these "articles hors bordereau". It will hold the article's code, designation, unit, quantity, unit price, and the calculated total amount for that line.

```java
@Entity
public class ArticleHorsBordereau {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String code;              // N° code of the article
    private String designation;       // Description of the article
    private String unite;             // Unit of the article
    private Double quantite = 0.0;    // Quantity in the project
    private Double prixUnitaire = 0.0; // Unit price
    private Double montant = 0.0;     // Total amount (quantite * prixUnitaire)

    @ManyToOne
    @JoinColumn(name = "projet_id", nullable = false)
    private Projet projet;            // The project this article is related to

    // Getters and Setters
}
```

Next, you will need a service method to read and process the Excel file, similar to how you handled the previous injection steps.

### 2. **Excel File Reader Logic**

You can create a method in a service class to read the "articles hors bordereau" file and populate the `ArticleHorsBordereau` list with the appropriate details. Here's a sample method for reading the Excel file:

```java
import org.apache.poi.ss.usermodel.*;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;

import java.io.InputStream;
import java.util.ArrayList;
import java.util.List;

@Service
public class ArticleHorsBordereauService {

    private final ArticleHorsBordereauRepository articleHorsBordereauRepository;

    @Autowired
    public ArticleHorsBordereauService(ArticleHorsBordereauRepository articleHorsBordereauRepository) {
        this.articleHorsBordereauRepository = articleHorsBordereauRepository;
    }

    public ExcelImportResult excelToArticleHorsBordereau(InputStream is, Long projetId) {
        List<ArticleHorsBordereau> articleHorsBordereauList = new ArrayList<>();
        List<SkippedRow> skippedRows = new ArrayList<>();

        try (Workbook workbook = new XSSFWorkbook(is)) {
            Sheet sheet = workbook.getSheetAt(0);
            if (sheet == null) throw new RuntimeException("Feuille introuvable");

            Row headerRow = sheet.getRow(0);
            if (headerRow == null) throw new RuntimeException("En-tête manquant");

            // Start reading the rows after the header
            for (int i = 1; i <= sheet.getLastRowNum(); i++) {
                Row row = sheet.getRow(i);
                if (row == null || isRowEmpty(row)) {
                    skippedRows.add(new SkippedRow(i + 1, "Ligne vide"));
                    continue;
                }

                String code = safeTrim(getCellValue(row.getCell(0))); // N°
                String designation = safeTrim(getCellValue(row.getCell(1))); // Designation
                String unite = safeTrim(getCellValue(row.getCell(2))); // Unite
                Double quantite = parseDouble(getCellValue(row.getCell(3))); // Quantité
                Double prixUnitaire = parseDouble(getCellValue(row.getCell(4))); // P.U
                Double montant = quantite * prixUnitaire; // Montant (quantite * prixUnitaire)

                // Validate and add to the list
                if (code.isEmpty() || designation.isEmpty() || quantite == 0 || prixUnitaire == 0) {
                    skippedRows.add(new SkippedRow(i + 1, "Données manquantes"));
                    continue;
                }

                ArticleHorsBordereau articleHorsBordereau = new ArticleHorsBordereau();
                articleHorsBordereau.setCode(code);
                articleHorsBordereau.setDesignation(designation);
                articleHorsBordereau.setUnite(unite);
                articleHorsBordereau.setQuantite(quantite);
                articleHorsBordereau.setPrixUnitaire(prixUnitaire);
                articleHorsBordereau.setMontant(montant);

                // Set the projet
                Projet projet = new Projet();  // You can retrieve the Projet from the database
                projet.setId(projetId); // Assuming the Projet ID is passed in
                articleHorsBordereau.setProjet(projet);

                articleHorsBordereauList.add(articleHorsBordereau);
            }
        } catch (Exception e) {
            throw new RuntimeException("Erreur de lecture du fichier: " + e.getMessage(), e);
        }

        // Save to the database
        articleHorsBordereauRepository.saveAll(articleHorsBordereauList);

        return new ExcelImportResult(articleHorsBordereauList, skippedRows);
    }

    // Helper methods for reading cells and parsing
    private String getCellValue(Cell cell) {
        return cell == null ? "" : cell.toString().trim();
    }

    private Double parseDouble(String value) {
        try {
            return value.isEmpty() ? 0.0 : Double.parseDouble(value.replace(",", "."));
        } catch (NumberFormatException e) {
            return 0.0;
        }
    }

    private boolean isRowEmpty(Row row) {
        return row == null || row.getPhysicalNumberOfCells() == 0;
    }

    private String safeTrim(String value) {
        return value == null ? "" : value.trim();
    }
}
```

### 3. **Repository for Saving Data**

Make sure you have a repository for `ArticleHorsBordereau` to handle the persistence:

```java
import org.springframework.data.jpa.repository.JpaRepository;

public interface ArticleHorsBordereauRepository extends JpaRepository<ArticleHorsBordereau, Long> {
}
```

### 4. **Excel Import Result**

You'll want to return some kind of result object (like `ExcelImportResult`) to indicate success/failure, as shown in your previous methods. Here\u2019s a simple version:

```java
public class ExcelImportResult {
    private List<ArticleHorsBordereau> importedArticles;
    private List<SkippedRow> skippedRows;

    // Constructor, getters and setters
}
```

### 5. **Skipped Row Class**

This class helps you keep track of rows that were skipped due to errors.

```java
public class SkippedRow {
    private int rowNum;
    private String reason;

    // Constructor, getters and setters
}
```

### 6. **Usage Example**

In your controller or service where you handle the file upload, you can call this method to read and save the "articles hors bordereau" from the Excel file:

```java
@Autowired
private ArticleHorsBordereauService articleHorsBordereauService;

public void handleFileUpload(InputStream fileInputStream, Long projetId) {
    ExcelImportResult result = articleHorsBordereauService.excelToArticleHorsBordereau(fileInputStream, projetId);
    // Handle result (e.g., show skipped rows, etc.)
}
```

### Conclusion

This approach will allow you to handle the "articles hors bordereau" file, read it, and store the details in your database. The file will include the article code, designation, unit, quantity, unit price, and the total amount, which will be calculated during the file processing.
