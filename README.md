Perfect \U0001f44c let\u2019s recap your situation clearly \u2014 then I\u2019ll show you exactly how to implement **the second stage (acheteur part)** in Spring Boot.

---

## \U0001f9e9 Context Recap

You have a **two-step Excel injection workflow**:

### \U0001f9cd\u200d\u2640\ufe0f 1\ufe0f\u20e3 Chef de projet injects file

* Columns:
  **`id`, `code`, `designation`, `unite`, `quantite`, `typeArticle`**
* No fournisseur or price.
* Data is stored in your `Article` (or `ArticleProjet`) table.

\u2705 You already implemented this part.

---

### \U0001f9cd\u200d\u2642\ufe0f 2\ufe0f\u20e3 Acheteur injects file

* The Excel contains **the same articles** (same `code` or `id`),
* plus **fournisseur(s)** and their **price(s)**.

Example columns:

| code | designation | unite | quantite | typeArticle | fournisseur | prix |
| ---- | ----------- | ----- | -------- | ----------- | ----------- | ---- |
| A001 | Câble       | m     | 500      | Électrique  | FO1         | 12.5 |
| A001 | Câble       | m     | 500      | Électrique  | FO2         | 11.7 |

You want to:
\u2705 Match by code (or id)
\u2705 Add each fournisseur + price to your database
\u2705 Without re-creating the article.

---

## \U0001f3d7\ufe0f Database structure suggestion

You\u2019ll have two entities:

```java
@Entity
public class Article {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String code;
    private String designation;
    private String unite;
    private Double quantite;
    private String typeArticle;

    @OneToMany(mappedBy = "article", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<ArticleFournisseur> fournisseurs = new ArrayList<>();
}
```

and

```java
@Entity
public class ArticleFournisseur {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String fournisseur;
    private Double prix;

    @ManyToOne
    @JoinColumn(name = "article_id")
    private Article article;
}
```

This allows an article to have **multiple fournisseurs** with their **price**.

---

## \U0001f9e0 Service logic for the acheteur\u2019s Excel

You\u2019ll parse the Excel file and for each row:

* Find existing article by code.
* Create or update fournisseur entries.

```java
@Service
@RequiredArgsConstructor
public class AcheteurExcelService {

    private final ArticleRepository articleRepository;
    private final ArticleFournisseurRepository fournisseurRepository;

    public void importFournisseurs(MultipartFile file) {
        try (Workbook workbook = new XSSFWorkbook(file.getInputStream())) {
            Sheet sheet = workbook.getSheetAt(0);

            for (int i = 1; i <= sheet.getLastRowNum(); i++) { // skip header
                Row row = sheet.getRow(i);
                if (row == null) continue;

                String code = getCellValue(row.getCell(0));
                String fournisseur = getCellValue(row.getCell(5));
                Double prix = Double.valueOf(getCellValue(row.getCell(6)));

                Article article = articleRepository.findByCode(code)
                        .orElseThrow(() -> new RuntimeException("Article non trouvé: " + code));

                ArticleFournisseur af = new ArticleFournisseur();
                af.setFournisseur(fournisseur);
                af.setPrix(prix);
                af.setArticle(article);

                fournisseurRepository.save(af);
            }
        } catch (IOException e) {
            throw new RuntimeException("Erreur de lecture du fichier Excel: " + e.getMessage());
        }
    }

    private String getCellValue(Cell cell) {
        if (cell == null) return "";
        if (cell.getCellType() == CellType.STRING) return cell.getStringCellValue();
        if (cell.getCellType() == CellType.NUMERIC) return String.valueOf(cell.getNumericCellValue());
        return "";
    }
}
```

---

## \U0001f9e9 Controller endpoint

```java
@RestController
@RequestMapping("/api/acheteur")
@RequiredArgsConstructor
public class AcheteurController {

    private final AcheteurExcelService acheteurExcelService;

    @PostMapping("/import-fournisseurs")
    public ResponseEntity<String> importFournisseurs(@RequestParam("file") MultipartFile file) {
        acheteurExcelService.importFournisseurs(file);
        return ResponseEntity.ok("Fournisseurs importés avec succès !");
    }
}
```

---

## \u2705 What this achieves

* Chef de projet uploads file \u2192 basic `Article` records saved.
* Acheteur uploads file \u2192 fournisseur + prix saved and linked to existing articles.
* No duplicates for articles.
* You can list articles with their fournisseurs easily using a DTO.

---

## Example of a response DTO

```java
@Data
public class ArticleDTO {
    private Long id;
    private String code;
    private String designation;
    private String unite;
    private Double quantite;
    private String typeArticle;
    private List<FournisseurDTO> fournisseurs;
}

@Data
public class FournisseurDTO {
    private String fournisseur;
    private Double prix;
}
```

---

Would you like me to include:

* \u2705 a ready-to-use ExcelHelper to read both parts (chef + acheteur) dynamically?
* \u2705 and a service that automatically detects if fournisseur columns exist?

That way, your backend will handle **both uploads with one endpoint** instead of two.
