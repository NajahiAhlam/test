Perfect \u2014 I understand exactly what you need now.
This is a **global calculation per supplier**, using:

---

# \u2705 **1. Estimation CC (main article estimation)**

Formula for each supplier:

```
Estimation_CC[supplier] = \u03a3 (quantite_articleProjet × prix_unitaire_supplier)
```

* `quantite_articleProjet` comes from **ArticleProjet**
* `prix_unitaire_supplier` comes from **ArticlePrix (or ProjetArticlePrix if overridden)**

If a price is null for a supplier \u2192
\u27a1 add this row into `ProjetPrixArticle`
\u27a1 use that price for calculation.

---

# \u2705 **2. Estimation prix hors-contrat (sum of all ArticleHorsBordereau total amounts)**

Formula:

```
Estimation_HB = \u03a3 montant (for all ArticleHorsBordereau)
```

This value is **the same for all suppliers**.

---

# \u2705 **3. TOTAL = Estimation_CC + Estimation_HB**

```
Total[supplier] = Estimation_CC[supplier] + Estimation_HB
```

---

# \u2705 **4. CLASSEMENT (ranking based on lowest Total)**

Sort totals asc:

```
1 = lowest total
2 = second lowest
...
```

---

# \U0001f4cc Final Result Format (exactly as you want)

|                         | Baticam | Gromarbre | Loft 2A | Decomarbre |
| ----------------------- | ------- | --------- | ------- | ---------- |
| Estimation CC           | 608,400 | 616,835   | 608,410 | 660,130    |
| Estimation hors-contrat | 60,500  | 60,500    | 60,500  | 60,500     |
| TOTAL                   | 668,900 | 677,335   | 668,910 | 720,630    |
| CLASSEMENT              | 1       | 2         | 3       | 4          |

---

# \U0001f525 NOW I GIVE YOU THE BACKEND CALCULATION SERVICE (FULL LOGIC)

You will get a **single method**:

```java
public EstimationResult calculateEstimation(Long projetId)
```

---

# \u2705 **1. Create DTO for the result**

```java
public class EstimationResult {
    private List<String> fournisseurs; // names
    private Map<String, Double> estimationCC; // per supplier
    private Map<String, Double> estimationHB; // per supplier
    private Map<String, Double> total;        // per supplier
    private Map<String, Integer> classement;  // ranks
}
```

---

# \u2705 **2. Service Method**

```java
@Override
public EstimationResult calculateEstimation(Long projetId) {

    Projet projet = projetRepository.findById(projetId)
            .orElseThrow(() -> new RuntimeException("Projet not found"));

    List<ArticleProjet> articleProjets = articleProjetRepository.findByProjetId(projetId);

    // All suppliers
    List<Fournisseur> fournisseurs = fournisseurRepository.findAll();

    // Initialize result maps
    Map<String, Double> estimationCC = new LinkedHashMap<>();
    Map<String, Double> estimationHB = new LinkedHashMap<>();
    Map<String, Double> total = new LinkedHashMap<>();
    Map<String, Integer> classement = new LinkedHashMap<>();

    // Init suppliers with 0
    fournisseurs.forEach(f -> estimationCC.put(f.getNom(), 0.0));

    // ===== 1. ESTIMATION CC (main calculation) =====
    for (ArticleProjet ap : articleProjets) {

        Article article = ap.getArticle();
        Double quantite = ap.getQuantite();

        // prices by supplier
        List<ArticlePrix> prixList = articlePrixRepository.findByArticleId(article.getId());

        for (ArticlePrix px : prixList) {
            String fournisseur = px.getFournisseur().getNom();
            Double prixUnit = px.getPrix() != null ? px.getPrix() : 0.0;

            // accumulate estimation
            estimationCC.put(
                fournisseur,
                estimationCC.get(fournisseur) + (prixUnit * quantite)
            );
        }
    }

    // ===== 2. ESTIMATION HORS CONTRAT =====

    Double hbTotal = articleHorsBordereauRepository.sumMontantsForProjet(projetId);

    fournisseurs.forEach(f -> estimationHB.put(f.getNom(), hbTotal));

    // ===== 3. TOTAL = CC + HB =====

    fournisseurs.forEach(f -> {
        Double t = estimationCC.get(f.getNom()) + estimationHB.get(f.getNom());
        total.put(f.getNom(), t);
    });

    // ===== 4. CLASSEMENT =====

    List<Map.Entry<String, Double>> sorted =
            total.entrySet().stream()
                    .sorted(Map.Entry.comparingByValue())
                    .collect(Collectors.toList());

    int rank = 1;
    for (Map.Entry<String, Double> e : sorted) {
        classement.put(e.getKey(), rank++);
    }

    // ===== Build response =====

    EstimationResult result = new EstimationResult();
    result.setFournisseurs(fournisseurs.stream().map(Fournisseur::getNom).collect(Collectors.toList()));
    result.setEstimationCC(estimationCC);
    result.setEstimationHB(estimationHB);
    result.setTotal(total);
    result.setClassement(classement);

    return result;
}
```

---

# \U0001f525 Done.

You now have:

### \u2714 Estimation CC

### \u2714 Estimation hors contrat

### \u2714 Total

### \u2714 Classement

### \u2714 Same format as the table you showed

---

# \u2705 If you want:

I can also generate:

### \U0001f539 `EstimationResultDTO`

### \U0001f539 Controller endpoint

### \U0001f539 Angular service + table UI generation

Just tell me **\u201cgenerate controller\u201d** or **\u201cgenerate Angular side\u201d**.
