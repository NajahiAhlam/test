Below is everything you asked for:

\u2705 **1 \u2014 Mapper ArticleProjet \u2194 ArticleProjetDTO**
\u2705 **2 \u2014 Service method getAllArticleProjet with filtering + pagination (same style as your getAllProjets)**

---

# \u2705 1. **Mapper for ArticleProjet**

### **ArticleProjetMapper.java**

```java
@Mapper(componentModel = "spring", uses = { ArticleMapper.class })
public interface ArticleProjetMapper {

    ArticleProjetDTO toDto(ArticleProjet entity);

    ArticleProjet toEntity(ArticleProjetDTO dto);
}
```

### If you need manual mapper:

```java
@Component
@RequiredArgsConstructor
public class ArticleProjetMapper {

    private final ArticleMapper articleMapper;

    public ArticleProjetDTO toDto(ArticleProjet entity) {
        if (entity == null) return null;

        ArticleProjetDTO dto = new ArticleProjetDTO();
        dto.setId(entity.getId());
        dto.setQuantite(entity.getQuantite());
        dto.setArticle(articleMapper.toDto(entity.getArticle()));
        return dto;
    }

    public ArticleProjet toEntity(ArticleProjetDTO dto) {
        if (dto == null) return null;

        ArticleProjet entity = new ArticleProjet();
        entity.setId(dto.getId());
        entity.setQuantite(dto.getQuantite());
        entity.setArticle(articleMapper.toEntity(dto.getArticle()));
        return entity;
    }
}
```

---

# \u2705 2. **getAllArticleProjet() with dynamic filtering + pagination**

Filtering possible on:

* `code` (article.code)
* `designation` (article.designation)
* `unite` (article.unite)
* `quantite`
* `projetId`
* `typeArticle`

### **ArticleProjetServiceImpl.java**

```java
@Override
public ObjectPagination<ArticleProjetDTO> getAllArticleProjet(
        int page, int size, String sortDirection, String sortValue, Map<String, String> params) {

    Specification<ArticleProjet> spec = (root, query, cb) -> {
        List<Predicate> predicates = new ArrayList<>();

        Join<ArticleProjet, Article> articleJoin = root.join("article");
        Join<ArticleProjet, Projet> projetJoin = root.join("projet");

        params.forEach((key, value) -> {
            String lowerValue = value.toLowerCase();

            switch (key) {

                case "code":
                    predicates.add(cb.like(cb.lower(articleJoin.get("code")), "%" + lowerValue + "%"));
                    break;

                case "designation":
                    predicates.add(cb.like(cb.lower(articleJoin.get("designation")), "%" + lowerValue + "%"));
                    break;

                case "unite":
                    predicates.add(cb.like(cb.lower(articleJoin.get("unite")), "%" + lowerValue + "%"));
                    break;

                case "typeArticle":
                    predicates.add(cb.like(cb.lower(articleJoin.get("typeArticle")), "%" + lowerValue + "%"));
                    break;

                case "quantite":
                    try {
                        Double q = Double.parseDouble(value);
                        predicates.add(cb.equal(root.get("quantite"), q));
                    } catch (NumberFormatException e) {
                        predicates.add(cb.equal(root.get("id"), -1)); // Return empty list
                    }
                    break;

                case "projetId":
                    try {
                        Long id = Long.parseLong(value);
                        predicates.add(cb.equal(projetJoin.get("id"), id));
                    } catch (NumberFormatException e) {
                        predicates.add(cb.equal(root.get("id"), -1));
                    }
                    break;

                default:
                    break;
            }
        });

        return cb.and(predicates.toArray(new Predicate[0]));
    };

    Sort sort = Sort.by(
            "ASC".equals(sortDirection) ? Sort.Direction.ASC : Sort.Direction.DESC,
            sortValue != null ? sortValue : "id"
    );

    Page<ArticleProjet> pageOfData = articleProjetRepository.findAll(spec, PageRequest.of(page, size, sort));

    ObjectPagination<ArticleProjetDTO> pagination = new ObjectPagination<>();
    pagination.setContent(pageOfData.stream()
            .map(articleProjetMapper::toDto)
            .collect(Collectors.toList()));
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

# \u2705 3. **articleProjetRepository with Specification**

```java
public interface ArticleProjetRepository extends JpaRepository<ArticleProjet, Long>, JpaSpecificationExecutor<ArticleProjet> {

    Optional<ArticleProjet> findByArticleAndProjet(Article article, Projet projet);
}
```

---

# If you want, I can also generate:

\u2705 Controller endpoint
\u2705 Front-end Angular service
\u2705 Filtering DTO
\u2705 Better performance version (with fetch joins)

Just tell me!
