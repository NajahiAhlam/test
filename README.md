Below is **exactly the structure you want**, with the backend returning **a list sorted by classement**, where **each fournisseur has its own block**:

```
[
  {
    "fournisseur": "baticam",
    "estimationCC": 412700.0,
    "estimationHB": 60500.0,
    "total": 473200.0,
    "classement": 1
  },
  {
    "fournisseur": "exibat",
    "estimationCC": 427670.0,
    "estimationHB": 60500.0,
    "total": 488170.0,
    "classement": 2
  },
  ...
]
```

---

# \u2705 **Step 1 \u2014 Create New DTO**

```java
public class EstimationFournisseurDTO {
    private String fournisseur;
    private Double estimationCC;
    private Double estimationHB;
    private Double total;
    private Integer classement;
}
```

And change the global return DTO:

```java
public class EstimationResult {
    private List<EstimationFournisseurDTO> result;
}
```

---

# \u2705 **Step 2 \u2014 Modify Method to Build Grouped + Sorted Structure**

Here is your **updated and correct method** with zero breakage to your logic, only restructuring the output:

```java
@Override
public EstimationResult calculateEstimation(Long projetId) {

    Projet projet = projetRepository.findById(projetId)
            .orElseThrow(() -> new RuntimeException("Projet not found"));

    List<ArticleProjet> articleProjets = articleProjetRepository.findByProjetId(projetId);

    List<Fournisseur> fournisseurs = fournisseurRepository.findAll();

    Map<String, Double> estimationCC = new LinkedHashMap<>();
    Map<String, Double> estimationHB = new LinkedHashMap<>();
    Map<String, Double> total = new LinkedHashMap<>();
    Map<String, Integer> classement = new LinkedHashMap<>();

    fournisseurs.forEach(f -> estimationCC.put(f.getRaisonSociale(), 0.0));

    // ===== 1. ESTIMATION CC =====
    for (Fournisseur f : fournisseurs) {

        double supplierTotal = 0.0;

        for (ArticleProjet ap : articleProjets) {

            Article article = ap.getArticle();
            Double quantite = ap.getQuantite();

            PriceArticle mainPrice = priceArticleRepository
                    .findByArticleIdAndFournisseurId(article.getId(), f.getId())
                    .orElse(null);

            Double prixUnit = (mainPrice != null && mainPrice.getPrice() != null)
                    ? mainPrice.getPrice()
                    : null;

            if (prixUnit == null) {
                ProjetPriceArticle projetPrice = projetPriceArticleRepository
                        .findByArticleProjetIdAndFournisseurId(ap.getId(), f.getId())
                        .orElse(null);

                prixUnit = projetPrice != null ? projetPrice.getPrice() : 0.0;
            }

            supplierTotal += (prixUnit * quantite);
        }

        estimationCC.put(f.getRaisonSociale(), supplierTotal);
    }

    // ===== 2. ESTIMATION HB =====

    Double hbTotal = articleHorsBordereauRepository.sumMontantsForProjet(projetId);

    fournisseurs.forEach(f -> estimationHB.put(f.getRaisonSociale(), hbTotal));

    // ===== 3. TOTAL =====

    fournisseurs.forEach(f -> {
        Double t = estimationCC.get(f.getRaisonSociale()) + estimationHB.get(f.getRaisonSociale());
        total.put(f.getRaisonSociale(), t);
    });

    // ===== 4. CLASSEMENT =====

    List<Map.Entry<String, Double>> sorted =
            total.entrySet().stream()
                    .sorted(Map.Entry.comparingByValue()) // ascending
                    .collect(Collectors.toList());

    int rank = 1;
    for (Map.Entry<String, Double> e : sorted) {
        classement.put(e.getKey(), rank++);
    }

    // ===== 5. Build Final Grouped Result =====

    List<EstimationFournisseurDTO> finalList = new ArrayList<>();

    for (Map.Entry<String, Integer> entry : classement.entrySet()) {
        String fournisseur = entry.getKey();

        EstimationFournisseurDTO dto = new EstimationFournisseurDTO();
        dto.setFournisseur(fournisseur);
        dto.setEstimationCC(estimationCC.get(fournisseur));
        dto.setEstimationHB(estimationHB.get(fournisseur));
        dto.setTotal(total.get(fournisseur));
        dto.setClassement(classement.get(fournisseur));

        finalList.add(dto);
    }

    // Sort again by classement to make sure order is correct
    finalList.sort(Comparator.comparing(EstimationFournisseurDTO::getClassement));

    // Final response container
    EstimationResult result = new EstimationResult();
    result.setResult(finalList);

    return result;
}
```

---

# \u2705 **Final Output Example**

```
{
  "result": [
    {
      "fournisseur": "baticam",
      "estimationCC": 412700.0,
      "estimationHB": 60500.0,
      "total": 473200.0,
      "classement": 1
    },
    {
      "fournisseur": "exibat",
      "estimationCC": 427670.0,
      "estimationHB": 60500.0,
      "total": 488170.0,
      "classement": 2
    },
    ...
  ]
}
```

Sorted correctly by classement.

---

# Want me to generate the Angular table that displays this sorted ranking?

With color badges for top 3, responsive layout, and Nebular styling?

Just say **"generate Angular table"**.
