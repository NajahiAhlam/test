Below is everything you asked for:

---

# \u2705 **1. ArticleHorsBordereauDTO**

```java
public class ArticleHorsBordereauDTO {
    private Long id;
    private String code;
    private String designation;
    private String unite;
    private Double quantite;
    private Double prixUnitaire;
    private Double montant;
    private Double totalMontant;
    private Long projetId;
}
```

---

# \u2705 **2. Mapper (Interface + Implementation)**

**Following exactly the style you already use in your project.**

### **Mapper Interface**

```java
public interface ArticleHorsBordereauMapper {

    ArticleHorsBordereauDTO toDto(ArticleHorsBordereau entity);

    ArticleHorsBordereau toEntity(ArticleHorsBordereauDTO dto);
}
```

---

### **Mapper Implementation**

```java
@Component
public class ArticleHorsBordereauMapperImpl implements ArticleHorsBordereauMapper {

    @Autowired
    private ProjetRepository projetRepository;

    @Override
    public ArticleHorsBordereauDTO toDto(ArticleHorsBordereau entity) {
        if (entity == null) return null;

        ArticleHorsBordereauDTO dto = new ArticleHorsBordereauDTO();

        dto.setId(entity.getId());
        dto.setCode(entity.getCode());
        dto.setDesignation(entity.getDesignation());
        dto.setUnite(entity.getUnite());
        dto.setQuantite(entity.getQuantite());
        dto.setPrixUnitaire(entity.getPrixUnitaire());
        dto.setMontant(entity.getMontant());
        dto.setTotalMontant(entity.getTotalMontant());
        dto.setProjetId(entity.getProjet() != null ? entity.getProjet().getId() : null);

        return dto;
    }

    @Override
    public ArticleHorsBordereau toEntity(ArticleHorsBordereauDTO dto) {
        if (dto == null) return null;

        ArticleHorsBordereau entity = new ArticleHorsBordereau();

        entity.setId(dto.getId());
        entity.setCode(dto.getCode());
        entity.setDesignation(dto.getDesignation());
        entity.setUnite(dto.getUnite());
        entity.setQuantite(dto.getQuantite());
        entity.setPrixUnitaire(dto.getPrixUnitaire());
        entity.setMontant(dto.getMontant());
        entity.setTotalMontant(dto.getTotalMontant());

        if (dto.getProjetId() != null) {
            Projet projet = projetRepository.findById(dto.getProjetId())
                    .orElseThrow(() -> new RuntimeException("Projet introuvable"));
            entity.setProjet(projet);
        }

        return entity;
    }
}
```

---

# \u2705 **3. Pagination + Filtering Method (Same style as your ArticleProjet method)**

Fully adapted to `ArticleHorsBordereau`.

```java
@Override
public ObjectPagination<ArticleHorsBordereauDTO> getAllArticleHorsBordereau(
        int page, int size, String sortDirection, String sortValue, Map<String, String> params) {

    Specification<ArticleHorsBordereau> spec = (root, query, cb) -> {
        List<Predicate> predicates = new ArrayList<>();

        Join<ArticleHorsBordereau, Projet> projetJoin = root.join("projet");

        params.forEach((key, value) -> {
            String lower = value.toLowerCase();

            switch (key) {

                case "code":
                    predicates.add(cb.like(cb.lower(root.get("code")), "%" + lower + "%"));
                    break;

                case "designation":
                    predicates.add(cb.like(cb.lower(root.get("designation")), "%" + lower + "%"));
                    break;

                case "unite":
                    predicates.add(cb.like(cb.lower(root.get("unite")), "%" + lower + "%"));
                    break;

                case "quantite":
                    try {
                        Double quantity = Double.parseDouble(value);
                        predicates.add(cb.equal(root.get("quantite"), quantity));
                    } catch (Exception e) {
                        predicates.add(cb.equal(root.get("id"), -1));
                    }
                    break;

                case "projetId":
                    try {
                        Long id = Long.parseLong(value);
                        predicates.add(cb.equal(projetJoin.get("id"), id));
                    } catch (Exception e) {
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
            "ASC".equalsIgnoreCase(sortDirection) ? Sort.Direction.ASC : Sort.Direction.DESC,
            sortValue != null ? sortValue : "id"
    );

    Page<ArticleHorsBordereau> pageOfData = articleHorsBordereauRepository
            .findAll(spec, PageRequest.of(page, size, sort));

    ObjectPagination<ArticleHorsBordereauDTO> pagination = new ObjectPagination<>();

    pagination.setContent(
            pageOfData.stream().map(articleHorsBordereauMapper::toDto).collect(Collectors.toList())
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

# \u26a1 Your backend is now complete with:

### \u2714 DTO

### \u2714 Mapper Interface

### \u2714 Mapper Implementation

### \u2714 Pagination + Filtering Service Method

If you want, I can also generate:
\u2705 Controller method
\u2705 Angular service
\u2705 Angular table filters
\u2705 Excel import service for hors-bordereau

Just tell me!
