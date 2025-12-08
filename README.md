From what you\u2019ve described, you\u2019re trying to handle two different types of Excel import scenarios, with the same `Article` and `Fournisseur` headers but varying `Designation` and `Price` formats. Below is a solution that can help you handle both the **Lot Unique** type and **Electricité** type articles with proper handling of their specific Excel structures.

### Understanding the Problem

1. **Lot Unique:**

   * You have a column for the article designation and several columns for the prices of different `Fournisseur` entities. The prices are directly listed next to each `Fournisseur` name, and each row corresponds to an article.

2. **Electricité (Lustrerie Agences):**

   * The `Designation` column is split into two parts: the first part is the article description, and the second part includes "Marque" and "Reference".
   * The prices for each brand (`Marque`) and `Reference` could be shared across multiple rows or spread across different columns.

### Solution Outline

To implement this logic in your backend, you\u2019ll need to:

1. **Parse the Excel data correctly:**

   * Handle both types of data (Lot Unique and Electricité).
   * Detect and split the `Designation` column if the format is for Electricité (i.e., containing "Marque" and "Reference").
   * Handle price assignment for each `Fournisseur` in the correct column.

2. **Update the Entities:**

   * For `Lot Unique`, each price will directly map to a `PriceArticle` object for the corresponding `Fournisseur`.
   * For `Electricité`, each `PriceArticle` should be linked to the specific brand and reference combination.

### 1. Parsing Excel Data

You need to loop through the rows in your Excel sheet, determine the article type (whether it's **Lot Unique** or **Electricité**), and then process it accordingly.

#### Sample Logic for Parsing

```java
import org.apache.poi.ss.usermodel.*;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;

public class ExcelImportService {

    public void importExcel(InputStream is) throws Exception {
        Workbook workbook = new XSSFWorkbook(is);
        Sheet sheet = workbook.getSheetAt(0); // Assuming data is in the first sheet

        for (Row row : sheet) {
            if (row.getRowNum() == 0) continue;  // Skip the header row
            
            String designation = getCellValue(row.getCell(1));  // Assuming the 'DESIGNATION' column is at index 1

            if (designation.contains("MARQUE") || designation.contains("REFERENCE")) {
                // Handle Electricité type
                processElectriciteRow(row);
            } else {
                // Handle Lot Unique type
                processLotUniqueRow(row);
            }
        }
    }

    private void processLotUniqueRow(Row row) {
        // Handle rows for Lot Unique
        String articleCode = getCellValue(row.getCell(0));  // 'N°'
        String articleDesignation = getCellValue(row.getCell(1));  // 'DESIGNATION'
        
        for (int i = 2; i < row.getLastCellNum(); i++) {
            String fournisseurName = getColumnHeader(i);  // Get the fournisseur from the header row (assuming it's the first row)
            Double price = getPriceFromCell(row.getCell(i));
            
            // Create PriceArticle entities and associate them with the Article and Fournisseur
            // Add logic to persist Article, Fournisseur, and PriceArticle
        }
    }

    private void processElectriciteRow(Row row) {
        // Handle rows for Electricité (Lustrerie Agences)
        String articleCode = getCellValue(row.getCell(0));  // 'N°'
        String articleDesignation = getCellValue(row.getCell(1));  // 'DESIGNATION'

        String marque = getCellValue(row.getCell(2));  // MARQUE
        String reference = getCellValue(row.getCell(3));  // REFERENCE
        
        for (int i = 4; i < row.getLastCellNum(); i++) {
            String fournisseurName = getColumnHeader(i);  // Get the fournisseur from the header row (assuming it's the first row)
            Double price = getPriceFromCell(row.getCell(i));
            
            // Create PriceArticle entities and associate them with the Article, Fournisseur, Marque, and Reference
            // Add logic to persist Article, Fournisseur, and PriceArticle
        }
    }

    private String getCellValue(Cell cell) {
        if (cell == null) return "";
        switch (cell.getCellType()) {
            case STRING: return cell.getStringCellValue();
            case NUMERIC: return String.valueOf(cell.getNumericCellValue());
            default: return "";
        }
    }

    private Double getPriceFromCell(Cell cell) {
        if (cell == null || cell.getCellType() != CellType.NUMERIC) return 0.0;
        return cell.getNumericCellValue();
    }

    private String getColumnHeader(int index) {
        // Assuming the header row is the first row in the sheet (index 0)
        Row headerRow = sheet.getRow(0);
        Cell headerCell = headerRow.getCell(index);
        return getCellValue(headerCell); // Get fournisseur name from header
    }
}
```

### 2. Saving to the Database

After parsing the Excel data:

1. For **Lot Unique**, you will save the article\u2019s prices and link them to the corresponding `Fournisseur`.
2. For **Electricité**, the same process occurs, but you also need to handle the `Marque` and `Reference` information as part of the `PriceArticle` entity.

For this, you can update your entities as follows:

```java
@Entity
public class PriceArticle {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne
    @JoinColumn(name = "article_id", nullable = false)
    private Article article;

    @ManyToOne
    @JoinColumn(name = "fournisseur_id", nullable = false)
    private Fournisseur fournisseur;

    private Double price;

    private String marque;  // Only used for Electricité
    private String reference;  // Only used for Electricité
}
```

### 3. Handling `Marque` and `Reference`

When handling Electricité articles, you will store the `marque` and `reference` information in the `PriceArticle` entity and handle them in the parsing logic accordingly.

### 4. Final Integration

Make sure that:

* You handle the mapping between `Article` and `Fournisseur` correctly.
* You distinguish between `Lot Unique` and `Electricité` articles during the import process and update the entities accordingly.

### Conclusion

The solution involves handling two different formats by parsing the `Designation` column correctly, identifying if it's a **Lot Unique** or **Electricité**, and then saving the correct price data and references in the database. The entity changes include adding `marque` and `reference` to the `PriceArticle` entity for Electricité.
