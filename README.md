Great \u2014 now I fully understand your workflow.
You have **TWO injections**:

---

# \u2705 **FIRST INJECTION (Already Done & Working)**

Input: Excel with
\u2714 code
\u2714 designation
\u2714 unite
\u2714 typeArticle
\u2714 Fournisseurs (dynamic columns) \u2192 prix (initially NULL)

You save:

### \u2714 `Article`

### \u2714 `Fournisseur`

### \u2714 `PriceArticle`

This works.

---

# \u2705 **SECOND INJECTION (Need New Structure + New Excel Reader)**

Input Excel contains:

\u2714 code
\u2714 designation
\u2714 unite
\u2714 typeArticle
\u2714 **quantity for each fournisseur** (updated prices inside projet)

But **this second injection must NOT create new articles**, because they already exist.

Instead you must update:

### 1. `ProjectArticle` (quantité of article per projet)

### 2. `PriceArticle` (for this projet only)

So we need **new entity** \u2192 `ProjetPriceArticle`.

Because **price changes per project**, not globally.

---

# \u2705 **FINAL MODEL: CORRECT STRUCTURE**

---

## **1\ufe0f\u20e3 Keep your existing `Article`, `Projet`, `Fournisseur`, `PriceArticle`**

No change.

---

## **2\ufe0f\u20e3 Add `ArticleProjet` (quantity per project)**

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

    private Double quantite = 0.0;
}
```

---

## **3\ufe0f\u20e3 Add NEW ENTITY `ProjetPriceArticle` (supplier price for a project)**

\u27a1 This is REQUIRED because supplier price changes PER PROJECT.

```java
@Entity
public class ProjetPriceArticle {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne
    @JoinColumn(name = "article_projet_id", nullable = false)
    private ArticleProjet articleProjet;

    @ManyToOne
    @JoinColumn(name = "fournisseur_id", nullable = false)
    private Fournisseur fournisseur;

    private Double price;
}
```

---

# \U0001f7e6 WHY THIS ENTITY IS NECESSARY?

Because the price in first injection = **reference price**
But price in second injection = **project-specific price**

So you cannot modify `PriceArticle`, or you lose the reference catalogue.

---

# \u26a1 NOW THE EXCEL READER FOR SECOND INJECTION

You will read:

| code | designation | unite | typeArticle | f1_qty | f2_qty | f3_qty | ... |

**Headers from column 4 onward are fournisseur names again.**

---

# \U0001f535 FULL SECOND EXCEL READER (Complete Implementation)

```java
public ExcelImportResult excelToProjetArticles(InputStream is, Long projetId) {

    Projet projet = projetRepository.findById(projetId)
            .orElseThrow(() -> new RuntimeException("Projet introuvable !"));

    List<ArticleProjet> articlesProjet = new ArrayList<>();
    List<SkippedRow> skipped = new ArrayList<>();

    try (Workbook workbook = new XSSFWorkbook(is)) {

        Sheet sheet = workbook.getSheet(SHEET_NAME);
        Row headerRow = sheet.getRow(0);

        int fournisseurStartIndex = 4;

        // read supplier headers
        List<String> fournisseursHeaders = new ArrayList<>();
        for (int i = fournisseurStartIndex; i < headerRow.getLastCellNum(); i++) {
            String f = safeTrim(getCellValue(headerRow.getCell(i)));
            if (!f.isEmpty()) fournisseursHeaders.add(f);
        }

        for (int i = 1; i <= sheet.getLastRowNum(); i++) {

            Row row = sheet.getRow(i);
            if (row == null || isRowEmpty(row)) continue;

            String code = safeTrim(getCellValue(row.getCell(COL_CODE)));
            if (code.isEmpty()) {
                skipped.add(new SkippedRow(i + 1, "Code article vide"));
                continue;
            }

            Article article = articleRepository.findByCode(code)
                    .orElse(null);

            if (article == null) {
                skipped.add(new SkippedRow(i + 1, "Article non trouvé : " + code));
                continue;
            }

            // Create or update ArticleProjet
            ArticleProjet ap = articleProjetRepository
                    .findByArticleAndProjet(article, projet)
                    .orElseGet(() -> {
                        ArticleProjet np = new ArticleProjet();
                        np.setArticle(article);
                        np.setProjet(projet);
                        return np;
                    });

            // read quantity (default 0)
            String qtyStr = safeTrim(getCellValue(row.getCell(4)));
            double qty = qtyStr.isEmpty() ? 0.0 : Double.parseDouble(qtyStr.replace(",", "."));
            ap.setQuantite(qty);

            ap = articleProjetRepository.save(ap);

            // read supplier prices
            for (int j = 0; j < fournisseursHeaders.size(); j++) {

                String fournisseurName = fournisseursHeaders.get(j);
                int cellIndex = fournisseurStartIndex + j;

                String priceStr = safeTrim(getCellValue(row.getCell(cellIndex)));
                if (priceStr.isEmpty()) continue;

                double prix = Double.parseDouble(priceStr.replace(",", "."));

                Fournisseur fournisseur = fournisseurRepository
                        .findByRaisonSocialeIgnoreCase(fournisseurName)
                        .orElse(null);

                if (fournisseur == null) continue;

                ProjetPriceArticle ppa = projetPriceArticleRepository
                        .findByArticleProjetAndFournisseur(ap, fournisseur)
                        .orElseGet(() -> {
                            ProjetPriceArticle x = new ProjetPriceArticle();
                            x.setArticleProjet(ap);
                            x.setFournisseur(fournisseur);
                            return x;
                        });

                ppa.setPrice(prix);

                projetPriceArticleRepository.save(ppa);
            }

            articlesProjet.add(ap);
        }

    } catch (Exception e) {
        throw new RuntimeException("Erreur import : " + e.getMessage());
    }

    return new ExcelImportResult(articlesProjet, skipped);
}
```

---

# \U0001f3af **WHAT THIS CODE DOES**

### \u2714 Reads second Excel

### \u2714 Matches article by `code`

### \u2714 Saves quantity in `ArticleProjet`

### \u2714 Reads suppliers (dynamic headers)

### \u2714 Saves project-specific prices in `ProjetPriceArticle`

### \u2714 If cell empty \u2192 price ignored

### \u2714 If quantity empty \u2192 `0.0` saved

---

# \U0001f7e2 Your system is now COMPLETE

### You now have:

### 1\ufe0f\u20e3 Catalogue:

* `Article`
* `Fournisseur`
* `PriceArticle` (reference)

### 2\ufe0f\u20e3 Project-specific data:

* `ArticleProjet` (quantité per project)
* `ProjetPriceArticle` (supplier prices per project)

---

If you want, I can also generate:

\u2705 DTOs
\u2705 Service + Controller
\u2705 Better error handling
\u2705 Vue/React/Angular uploader
Just tell me!
