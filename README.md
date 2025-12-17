To make your `importArticlesFromExcel` method cleaner, we'll **extract the logic into helper methods** and **maintain clear separation of concerns**. I'll refactor it into smaller, more readable methods and fix the main method accordingly.

Here\u2019s the approach:

* We'll keep the `@Transactional` annotation on the main method.
* The logic for **deactivating articles** and **processing Excel data** will be split into separate helper methods, each focusing on a single responsibility.

---

### \u2705 Refactored Code:

#### 1\ufe0f\u20e3 Main Method (`importArticlesFromExcel`)

```java
@Override
@Transactional
public void importArticlesFromExcel(MultipartFile file) {
    try {
        validateExcelFile(file);
        deactivateExistingArticles();

        ExcelArticleReader.ExcelImportResult result = excelArticleReader.excelToArticles(file.getInputStream());
        validateImportResult(result);

        articleRepository.saveAll(result.getArticles());
        logSkippedRows(result.getSkippedRows());

    } catch (IOException e) {
        throw new RuntimeException("Failed to store Excel data: " + e.getMessage(), e);
    }
}
```

#### 2\ufe0f\u20e3 Helper Methods

**File Validation** (Ensures file format is valid)

```java
private void validateExcelFile(MultipartFile file) {
    if (!ExcelArticleReader.hasExcelFormat(file)) {
        throw new IllegalArgumentException("Invalid file format. Please upload an Excel (.xlsx) file.");
    }
}
```

**Deactivate Existing Articles** (Marks all articles as inactive)

```java
private void deactivateExistingArticles() {
    List<Article> existingArticles = articleRepository.findAll();
    if (existingArticles.isEmpty()) {
        return;
    }

    existingArticles.forEach(this::deactivateArticle);
    articleRepository.saveAll(existingArticles);
}
```

**Deactivate Individual Article** (Marks an article and its price articles as inactive)

```java
private void deactivateArticle(Article article) {
    article.setActive(false);

    if (article.getPriceArticles() != null) {
        article.getPriceArticles()
                .forEach(priceArticle -> priceArticle.setActive(false));
    }
}
```

**Validate Import Result** (Checks if Excel import data is valid)

```java
private void validateImportResult(ExcelArticleReader.ExcelImportResult result) {
    if (result == null || result.getArticles().isEmpty()) {
        throw new IllegalArgumentException("Le fichier Excel est vide ou ne contient aucune donnée valide.");
    }
}
```

**Log Skipped Rows** (Logs any skipped rows from the import)

```java
private void logSkippedRows(List<ExcelArticleReader.SkippedRow> skippedRows) {
    if (skippedRows != null && !skippedRows.isEmpty()) {
        skippedRows.forEach(s -> log.warn(s.toString()));
        log.info("*************************** {} lignes ignorées lors de l'import.", skippedRows.size());
    }
}
```

---

### \u2705 Key Changes:

1. **Helper Methods**:

   * `validateExcelFile`: Ensures the file is in the correct format.
   * `deactivateExistingArticles`: Deactivates all existing articles.
   * `deactivateArticle`: Deactivates an individual article and its price entries.
   * `validateImportResult`: Ensures the Excel data has articles and isn't empty.
   * `logSkippedRows`: Logs any skipped rows during the import process.

2. **Main Method Refactor**:

   * **Simplified flow**: Each method now does one job, and the main method orchestrates the process without getting too deep into nested logic.
   * **Transaction safety**: The `@Transactional` ensures all changes are rolled back in case of any failure.

---

### \u2705 Why This is Good

* **Sonar-compliant**: No deep nesting, clear and concise methods.
* **Single Responsibility Principle**: Each method is now focused on doing a single job.
* **Improved readability**: The main method is cleaner and easier to follow.
* **Maintainability**: Smaller methods are easier to test, debug, and maintain.

---

Let me know if you'd like any further tweaks, such as handling **exceptions** more granularly or adding logging for specific steps!
