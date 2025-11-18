Perfect \u2014 I will generate for you **the full equivalent of your getAllArticleProjet()**, but adapted for **ProjetPriceArticle**, including:

\u2705 **Service method getAllProjetPriceArticle(...)**
\u2705 **Specification filtering logic**
\u2705 **Mapper: ProjetPriceArticleMapper + DTO**
\u2705 **Pagination response ObjectPagination<ProjetPriceArticleDTO>**

Everything ready to paste.

---

# \u2705 1. ProjetPriceArticleDTO

```java
public class ProjetPriceArticleDTO {

    private Long id;

    private Long articleProjetId;
    private String articleCode;
    private String articleDesignation;

    private Long fournisseurId;
    private String fournisseurName;

    private Double price;
}
```

---

# \u2705 2. ProjetPriceArticleMapper

```java
@Component
public class ProjetPriceArticleMapper {

    public ProjetPriceArticleDTO toDto(ProjetPriceArticle entity) {
        if (entity == null) return null;

        ProjetPriceArticleDTO dto = new ProjetPriceArticleDTO();
        dto.setId(entity.getId());

        if (entity.getArticleProjet() != null) {
            dto.setArticleProjetId(entity.getArticleProjet().getId());
            dto.setArticleCode(entity.getArticleProjet().getArticle().getCode());
            dto.setArticleDesignation(entity.getArticleProjet().getArticle().getDesignation());
        }

        if (entity.getFournisseur() != null) {
            dto.setFournisseurId(entity.getFournisseur().getId());
            dto.setFournisseurName(entity.getFournisseur().getRaisonSociale());
        }

        dto.setPrice(entity.getPrice());

        return dto;
    }

    public ProjetPriceArticle toEntity(ProjetPriceArticleDTO dto) {
        // Only use this if you need create/update
        ProjetPriceArticle entity = new ProjetPriceArticle();
        entity.setId(dto.getId());

        // The service will need to fetch ArticleProjet and Fournisseur
        return entity;
    }
}
```

---

# \u2705 3. getAllProjetPriceArticle (FULL METHOD LIKE YOUR EXAMPLE)

This mirrors your **getAllArticleProjet** but adapted to **ProjetPriceArticle**
and joins the required references:

* articleProjet
* article
* fournisseur
* projet (through articleProjet)

\U0001f449 Supports filters on:

* projectId
* fournisseurId
* article code
* article designation
* price

Here is the full method:

```java
@Override
public ObjectPagination<ProjetPriceArticleDTO> getAllProjetPriceArticle(
        int page, int size, String sortDirection, String sortValue, Map<String, String> params) {

    Specification<ProjetPriceArticle> spec = (root, query, cb) -> {
        List<Predicate> predicates = new ArrayList<>();

        Join<ProjetPriceArticle, ArticleProjet> apJoin = root.join("articleProjet");
        Join<ArticleProjet, Article> articleJoin = apJoin.join("article");
        Join<ArticleProjet, Projet> projetJoin = apJoin.join("projet");
        Join<ProjetPriceArticle, Fournisseur> fournisseurJoin = root.join("fournisseur");

        params.forEach((key, value) -> {
            String lowerValue = value == null ? null : value.toLowerCase();

            switch (key) {

                case "projectId":
                    predicates.add(cb.equal(projetJoin.get("id"), Long.parseLong(value)));
                    break;

                case "fournisseurId":
                    predicates.add(cb.equal(fournisseurJoin.get("id"), Long.parseLong(value)));
                    break;

                case "code":
                    predicates.add(cb.like(cb.lower(articleJoin.get("code")), "%" + lowerValue + "%"));
                    break;

                case "designation":
                    predicates.add(cb.like(cb.lower(articleJoin.get("designation")), "%" + lowerValue + "%"));
                    break;

                case "price":
                    predicates.add(cb.equal(root.get("price"), Double.parseDouble(value)));
                    break;

                default:
                    break;
            }
        });

        return cb.and(predicates.toArray(new Predicate[0]));
    };

    Sort sort = Sort.by(
            "ASC".equalsIgnoreCase(sortDirection) ? Sort.Direction.ASC : Sort.Direction.DESC,
            (sortValue != null) ? sortValue : "id"
    );

    Page<ProjetPriceArticle> pageOfData =
            projetPriceArticleRepository.findAll(spec, PageRequest.of(page, size, sort));

    ObjectPagination<ProjetPriceArticleDTO> pagination = new ObjectPagination<>();
    pagination.setContent(
            pageOfData.stream()
                    .map(projetPriceArticleMapper::toDto)
                    .collect(Collectors.toList())
    );
    pagination.setLast(pageOfData.isLast());
    pagination.setFirst(pageOfData.isFirst());
    pagination.setTotalElements((int) pageOfData.getTotalElements());
    pagination.setTotalPages(pageOfData.getTotalPages());
    pagination.setSize(pageOfData.getSize());
    pagination.setNumber(pageOfData.getNumber());

    return pagination;
}
```

---

# \u2705 4. Repository (if needed)

```java
public interface ProjetPriceArticleRepository extends JpaRepository<ProjetPriceArticle, Long>, JpaSpecificationExecutor<ProjetPriceArticle> {
}
```

---

# \U0001f525 Your method supports filters like:

| Parameter     | Meaning                    | Example             |
| ------------- | -------------------------- | ------------------- |
| projectId     | filter by project          | `?projectId=12`     |
| fournisseurId | filter by supplier         | `?fournisseurId=3`  |
| code          | filter by article code     | `?code=ABC`         |
| designation   | filter article designation | `?designation=pipe` |
| price         | filter by exact price      | `?price=120.0`      |

---

# Want me to generate:

\u2705 Controller method?
\u2705 Angular frontend service + table columns?
\u2705 Postman request examples?

Just tell me!
