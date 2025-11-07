Perfect \U0001f44d You\u2019re using Apache POI, and your current helper is already quite powerful \u2014 it reads headers dynamically (for fournisseurs) and handles numeric/string cells safely.

Let\u2019s now build a **clean, modular, and professional version** of your Excel reader specifically for your new entity **`Article`**, following your Spring Boot \u201cby feature\u201d structure.

---

## \u2705 Goal

We\u2019ll make a clean class:

* Reads Excel files uploaded by the **chef projet** (no supplier columns yet).
* Uses **Apache POI**.
* Validates and maps each row to an `Article` entity.
* Uses helper methods for cleaner code and testability.

---

## \U0001f4c2 Package location

Since this belongs to the adjudication feature:

```
adjudication/
\u2514\u2500\u2500 util/
    \u2514\u2500\u2500 ArticleExcelHelper.java
```

---

## \U0001f9e9 Full Implementation

```java
package com.sgma.adjudication.util;

import com.sgma.adjudication.model.Article;
import org.apache.poi.ss.usermodel.*;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;
import org.springframework.stereotype.Service;
import org.springframework.web.multipart.MultipartFile;

import java.io.IOException;
import java.io.InputStream;
import java.util.ArrayList;
import java.util.List;

@Service
public class ArticleExcelHelper {

    private static final String EXCEL_TYPE = "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet";
    private static final String SHEET_NAME = "Feuil1";

    // Column order expected in Excel file
    private static final int COL_CODE = 0;
    private static final int COL_DESIGNATION = 1;
    private static final int COL_UNITE = 2;
    private static final int COL_QUANTITE = 3;
    private static final int COL_TYPE_ARTICLE = 4;

    /**
     * Check if the uploaded file is a valid Excel file.
     */
    public static boolean hasExcelFormat(MultipartFile file) {
        return EXCEL_TYPE.equals(file.getContentType());
    }

    /**
     * Read an Excel file and convert rows into a list of Article entities.
     */
    public List<Article> excelToArticles(InputStream is) {
        List<Article> articles = new ArrayList<>();

        try (Workbook workbook = new XSSFWorkbook(is)) {
            Sheet sheet = workbook.getSheet(SHEET_NAME);
            if (sheet == null) {
                throw new RuntimeException("Sheet '" + SHEET_NAME + "' not found in Excel file.");
            }

            // Skip header row (index 0)
            for (int i = 1; i <= sheet.getLastRowNum(); i++) {
                Row row = sheet.getRow(i);
                if (row == null || isRowEmpty(row)) continue;

                Article article = new Article();

                article.setCode(getCellValue(row.getCell(COL_CODE)));
                article.setDesignation(getCellValue(row.getCell(COL_DESIGNATION)));
                article.setUnite(getCellValue(row.getCell(COL_UNITE)));
                article.setQuantite(getCellValue(row.getCell(COL_QUANTITE)));
                article.setTypeArticle(getCellValue(row.getCell(COL_TYPE_ARTICLE)));

                articles.add(article);
            }

        } catch (IOException e) {
            throw new RuntimeException("Failed to parse Excel file: " + e.getMessage(), e);
        }

        return articles;
    }

    /**
     * Utility: Get cell value as a String, handling both numeric and string cells safely.
     */
    private String getCellValue(Cell cell) {
        if (cell == null) return "";

        switch (cell.getCellType()) {
            case STRING:
                return cell.getStringCellValue().trim();
            case NUMERIC:
                // Format numeric without scientific notation
                if (DateUtil.isCellDateFormatted(cell)) {
                    return cell.getDateCellValue().toString();
                }
                double numericValue = cell.getNumericCellValue();
                if (numericValue == Math.floor(numericValue)) {
                    return String.valueOf((long) numericValue); // integer-like number
                } else {
                    return String.valueOf(numericValue);
                }
            case BOOLEAN:
                return String.valueOf(cell.getBooleanCellValue());
            default:
                return "";
        }
    }

    /**
     * Utility: Check if a row is completely empty.
     */
    private boolean isRowEmpty(Row row) {
        if (row == null) return true;

        for (int c = 0; c < 5; c++) { // check first 5 columns only (code to typeArticle)
            Cell cell = row.getCell(c);
            if (cell != null && cell.getCellType() != CellType.BLANK && !getCellValue(cell).isEmpty()) {
                return false;
            }
        }
        return true;
    }
}
```

---

## \U0001f4d6 Explanation of structure

| Function          | Responsibility                                                    |
| ----------------- | ----------------------------------------------------------------- |
| `hasExcelFormat`  | Verifies if uploaded file is a valid `.xlsx`                      |
| `excelToArticles` | Reads all rows and maps them to `Article` entities                |
| `getCellValue`    | Converts any cell type (string, numeric, boolean, date) to String |
| `isRowEmpty`      | Skips empty rows safely                                           |

---

## \U0001f9e0 Example Usage in Service

Inside your `ArticleService.java`:

```java
@Service
public class ArticleService {

    private final ArticleRepository articleRepository;
    private final ArticleExcelHelper articleExcelHelper;

    public ArticleService(ArticleRepository articleRepository, ArticleExcelHelper articleExcelHelper) {
        this.articleRepository = articleRepository;
        this.articleExcelHelper = articleExcelHelper;
    }

    public void importArticlesFromExcel(MultipartFile file) {
        try {
            if (!ArticleExcelHelper.hasExcelFormat(file)) {
                throw new IllegalArgumentException("Invalid file format. Please upload an Excel (.xlsx) file.");
            }

            List<Article> articles = articleExcelHelper.excelToArticles(file.getInputStream());
            articleRepository.saveAll(articles);

        } catch (IOException e) {
            throw new RuntimeException("Failed to store Excel data: " + e.getMessage(), e);
        }
    }
}
```

---

## \U0001f9ea Example Excel File

| Code | Designation | Unité | Quantité | Type d'article |
| ---- | ----------- | ----- | -------- | -------------- |
| A001 | Câble RJ45  | mètre | 100      | Électrique     |
| A002 | Clavier USB | unité | 20       | Informatique   |

---

## \U0001f680 Summary

\u2705 Clean structure with clear separation of logic
\u2705 Uses the same **Apache POI** library
\u2705 Works seamlessly with your `Article` entity and `ArticleService`
\u2705 Ready for step 1 (chef projet upload)

---

If you\u2019d like, I can now add the **ExcelReader for the second stage (acheteur)** that reads the same file **plus supplier names and prices** dynamically (like your original helper) \u2014 and maps them to `PrixArticle` entities linked to `Article` and `Fournisseur`.
Would you like me to generate that next?
