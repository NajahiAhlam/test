To implement this functionality, you will need to adapt the data model, Excel file reading logic, and save the updated `quantite` values in the database while keeping the existing article prices intact.

Let me break it down into the following steps:

---

### 1. **Data Model**

Since the `quantite` for each `Article` changes per project, you will need to store the `quantite` for each article in the context of the specific project. This means we need to create a new entity `ArticleProjet` to link `Article` and `Projet` with a `quantite`.

**New Entity: ArticleProjet**

```java
@Entity
public class ArticleProjet {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne
    @JoinColumn(name = "article_id", nullable = false)
    private Article article;

    @ManyToOne
    @JoinColumn(name = "projet_id", nullable = false)
    private Projet projet;

    private Double quantite = 0.0;  // default to 0 if null

    @ManyToOne
    @JoinColumn(name = "fournisseur_id", nullable = true)
    private Fournisseur fournisseur;

    // Getters and Setters
}
```

* This `ArticleProjet` entity represents a relation between the `Article` and `Projet` entities, along with a `quantite` and optional `fournisseur` information.

---

### 2. **Excel Reader Logic**

You will now read the Excel file, identify the `Article` based on the `code` and `designation`, and update the `quantite` for the corresponding `Projet`. If the `quantite` value is missing, we will save it as `0.0`.

Let\u2019s assume the Excel structure is like this:

| Code | Designation | Unite | Quantite | Fournisseur 1 | Fournisseur 2 | Fournisseur 3 | Fournisseur 4 |
| ---- | ----------- | ----- | -------- | ------------- | ------------- | ------------- | ------------- |
| A123 | Article 1   | m2    | 100      | 500           | 600           | 550           | 650           |
| B456 | Article 2   | pcs   | 200      | 300           | 350           | 325           | 375           |

We will map this Excel data to the `ArticleProjet` entity.

### 3. **Code for Excel Reader**

```java
public class ExcelImportService {

    @Autowired
    private ArticleRepository articleRepository;

    @Autowired
    private ProjetRepository projetRepository;

    @Autowired
    private FournisseurRepository fournisseurRepository;

    @Autowired
    private ArticleProjetRepository articleProjetRepository;

    public void importQuantiteExcel(InputStream is, Long projetId) {
        try (Workbook workbook = new XSSFWorkbook(is)) {
            Sheet sheet = workbook.getSheetAt(0);
            Projet projet = projetRepository.findById(projetId)
                    .orElseThrow(() -> new RuntimeException("Projet introuvable"));

            for (int i = 1; i <= sheet.getLastRowNum(); i++) {
                Row row = sheet.getRow(i);
                if (row == null || isRowEmpty(row)) {
                    continue;
                }

                String code = getCellValue(row.getCell(0));
                String designation = getCellValue(row.getCell(1));
                String unite = getCellValue(row.getCell(2));
                String quantiteStr = getCellValue(row.getCell(3));
                Double quantite = quantiteStr != null && !quantiteStr.isEmpty() ? Double.parseDouble(quantiteStr) : 0.0;

                Article article = articleRepository.findByCodeAndDesignation(code, designation)
                        .orElseThrow(() -> new RuntimeException("Article introuvable: " + code));

                // If the article exists, update its quantite in the given projet context
                ArticleProjet articleProjet = new ArticleProjet();
                articleProjet.setArticle(article);
                articleProjet.setProjet(projet);
                articleProjet.setQuantite(quantite);

                // Check if there are fournisseur columns, optional
                for (int j = 4; j < row.getLastCellNum(); j++) {
                    String prixStr = getCellValue(row.getCell(j));
                    if (prixStr != null && !prixStr.isEmpty()) {
                        Double prix = Double.parseDouble(prixStr);
                        Fournisseur fournisseur = fournisseurRepository.findById((long) (j - 3)) // Assuming fournisseur IDs are sequential.
                                .orElseThrow(() -> new RuntimeException("Fournisseur introuvable"));
                        articleProjet.setFournisseur(fournisseur);
                    }
                }

                articleProjetRepository.save(articleProjet);
            }

        } catch (IOException e) {
            throw new RuntimeException("Erreur lors de la lecture du fichier Excel", e);
        }
    }

    private String getCellValue(Cell cell) {
        if (cell == null) {
            return null;
        }
        switch (cell.getCellType()) {
            case STRING:
                return cell.getStringCellValue().trim();
            case NUMERIC:
                return String.valueOf(cell.getNumericCellValue());
            default:
                return null;
        }
    }

    private boolean isRowEmpty(Row row) {
        for (int i = 0; i < row.getLastCellNum(); i++) {
            if (row.getCell(i) != null && row.getCell(i).toString().trim().length() > 0) {
                return false;
            }
        }
        return true;
    }
}
```

---

### 4. **Explanation of the Logic**

* **Excel Parsing**: We read the Excel file and check each row for the article\u2019s code, designation, and quantity. We also look for optional fournisseur prices.

* **Quantite Parsing**: If the quantity (`quantite`) column is empty or null, we default it to `0.0`.

* **Mapping to ArticleProjet**: For each valid row, we fetch the corresponding `Article` based on the code and designation. We then create or update an `ArticleProjet` for the given project (`projetId`), storing the updated `quantite`.

* **Fournisseur Handling**: For each fournisseur price, we link it with the `ArticleProjet` entity. The column indices assume a sequential numbering of fournisseurs. Adjust this based on your actual Excel layout.

---

### 5. **Update in `Article` and `PriceArticle`**

If you need to update or create new prices for `Article` related to a particular `Fournisseur`, ensure that `PriceArticle` is also properly updated. You can check the logic for handling prices, similar to how you handle quantities, if required.

---

### 6. **Calling the Import Method**

Once your backend logic is ready, you can invoke the `importQuantiteExcel` method when you upload the new Excel file from the frontend:

```java
importQuantiteExcel(fileInputStream, projetId);
```

---

## Summary of Updates:

* **New Entity**: `ArticleProjet` links `Article` to `Projet` and contains the `quantite`.
* **Excel Reading**: The Excel file is parsed, and `quantite` is saved in the `ArticleProjet` table. If `quantite` is missing, it is defaulted to `0.0`.
* **Handling Fournisseurs**: Prices for each fournisseur can be added as optional data.

This should allow you to update `quantite` values while preserving the article data and handling dynamic supplier prices.

Would you like further clarification on any part of the solution?
******************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************


Thanks for the clarification! Since the second Excel file only contains the `quantite` values for the articles (with no fournisseur prices), we will focus on updating only the `quantite` for each article in the context of the project.

### Revised Plan:

1. **Excel Structure**: The second file will only have the `code`, `designation`, `unite`, and `quantite` columns. Each row will correspond to an article, and you will update its `quantite` value for the specified `projetId`. The `fournisseur` and `price` data will remain unaffected.

2. **Data Model**: The existing `Article` entity stays the same, and we will still use the `ArticleProjet` entity to link `Article` and `Projet` with the updated `quantite`.

3. **Excel Reader**: You will read the second Excel file, which will contain only `code`, `designation`, `unite`, and `quantite`, and update the `quantite` for the appropriate `Article` and `Projet`.

---

### 1. **Entity Update:**

No changes to the entities are necessary. The `Article` and `ArticleProjet` entities remain as they are, with the `ArticleProjet` being the table that holds the `quantite` for each `Article` in the context of a `Projet`.

---

### 2. **Updated Excel File Structure (Second File)**:

This is the structure for the second Excel file:

| Code | Designation | Unite | Quantite |
| ---- | ----------- | ----- | -------- |
| A123 | Article 1   | m2    | 100      |
| B456 | Article 2   | pcs   | 200      |

In this case, you will **update only the `quantite`** for each `Article` in the `ArticleProjet` entity for the given `projetId`. If the `quantite` is missing, it should default to `0`.

---

### 3. **Excel Reader Code:**

Here\u2019s the code to read the second Excel file and update the `quantite`:

```java
public class ExcelImportService {

    @Autowired
    private ArticleRepository articleRepository;

    @Autowired
    private ProjetRepository projetRepository;

    @Autowired
    private ArticleProjetRepository articleProjetRepository;

    public void importQuantiteExcel(InputStream is, Long projetId) {
        try (Workbook workbook = new XSSFWorkbook(is)) {
            Sheet sheet = workbook.getSheetAt(0);
            Projet projet = projetRepository.findById(projetId)
                    .orElseThrow(() -> new RuntimeException("Projet introuvable"));

            for (int i = 1; i <= sheet.getLastRowNum(); i++) {
                Row row = sheet.getRow(i);
                if (row == null || isRowEmpty(row)) {
                    continue;
                }

                String code = getCellValue(row.getCell(0));
                String designation = getCellValue(row.getCell(1));
                String unite = getCellValue(row.getCell(2));
                String quantiteStr = getCellValue(row.getCell(3));
                Double quantite = quantiteStr != null && !quantiteStr.isEmpty() ? Double.parseDouble(quantiteStr) : 0.0;

                // Find the article based on code and designation
                Article article = articleRepository.findByCodeAndDesignation(code, designation)
                        .orElseThrow(() -> new RuntimeException("Article introuvable: " + code));

                // Find the ArticleProjet based on article and projet, or create a new one
                ArticleProjet articleProjet = articleProjetRepository.findByArticleAndProjet(article, projet)
                        .orElse(new ArticleProjet());  // If not found, create a new one

                articleProjet.setArticle(article);
                articleProjet.setProjet(projet);
                articleProjet.setQuantite(quantite);

                // Save or update the ArticleProjet entity with the new quantite
                articleProjetRepository.save(articleProjet);
            }

        } catch (IOException e) {
            throw new RuntimeException("Erreur lors de la lecture du fichier Excel", e);
        }
    }

    private String getCellValue(Cell cell) {
        if (cell == null) {
            return null;
        }
        switch (cell.getCellType()) {
            case STRING:
                return cell.getStringCellValue().trim();
            case NUMERIC:
                return String.valueOf(cell.getNumericCellValue());
            default:
                return null;
        }
    }

    private boolean isRowEmpty(Row row) {
        for (int i = 0; i < row.getLastCellNum(); i++) {
            if (row.getCell(i) != null && row.getCell(i).toString().trim().length() > 0) {
                return false;
            }
        }
        return true;
    }
}
```

---

### 4. **Explanation of the Code**:

1. **Read the Excel**: We use Apache POI to read the Excel file. The `code`, `designation`, `unite`, and `quantite` are extracted from each row.

2. **Find the Article**: We use the `code` and `designation` to find the corresponding `Article`. If no `Article` is found, an error is thrown.

3. **Update or Create `ArticleProjet`**: The `ArticleProjet` entity links the `Article` and `Projet`, and we update its `quantite` value. If the `ArticleProjet` entity doesn\u2019t exist for the given `Article` and `Projet`, we create a new one.

4. **Default `quantite`**: If the `quantite` value is missing or null in the Excel file, we default it to `0.0`.

5. **Save to Database**: The `ArticleProjet` entity is saved using the `articleProjetRepository.save()` method, which either updates or creates the record depending on whether it already exists.

---

### 5. **How to Use This Service:**

Once your backend logic is in place, you can call the `importQuantiteExcel` method to process the second Excel file.

For example, if you have a file `inputStream` from the frontend, call the service as follows:

```java
importQuantiteExcel(inputStream, projetId);
```

This will read the Excel file, update the quantities, and save the changes to the database.

---

### 6. **What this Solves:**

* The second file updates only the `quantite` for the articles linked to the specified project (`projetId`).
* The `fournisseur` and `price` information remain untouched.
* If the `quantite` is not specified in the Excel file, it defaults to `0.0`.

---

### 7. **Next Steps**:

If you have any more requirements, like validation for specific columns or handling errors in a more user-friendly way, feel free to let me know! Otherwise, this should solve the problem you're facing with updating quantities in your project context.

Does this work for your use case, or would you like to adjust anything further?

