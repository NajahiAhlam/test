public class ValidateurReportDTO {
    private String nom;
    private String prenom;
    private List<RisqueDTO> risques;
}

public class RisqueDTO {
    private String name;
    private List<RisqueInstanceDTO> instances;
}

public class RisqueInstanceDTO {
    private Long id;
    private String name;
    private DemandeQualificationDTO demande;
    private List<ConditionDTO> conditions;
}

public class ConditionDTO {
    private Long id;
    private String categorie;
    private String detail;
    private LocalDate dateEcheance;
    private LocalDate dateLancement;
    private Long numberSemaineApresLancement;
    private String etat;
}
public List<ValidateurReportDTO> generateRisqueReport(LocalDate start, LocalDate end) {
    CriteriaBuilder cb = entityManager.getCriteriaBuilder();
    CriteriaQuery<Risque> cq = cb.createQuery(Risque.class);
    Root<Risque> risqueRoot = cq.from(Risque.class);

    // Join associations
    Join<Risque, RisqueInstance> instanceJoin = risqueRoot.join("risqueInstances", JoinType.LEFT);
    Join<RisqueInstance, Conditions> conditionJoin = instanceJoin.join("conditions", JoinType.LEFT);
    Join<RisqueInstance, DemandeQualification> demandeJoin = instanceJoin.join("demandeQualification", JoinType.LEFT);

    List<Predicate> predicates = new ArrayList<>();

    if (start != null && end != null) {
        Predicate postCondition = cb.and(
            cb.equal(conditionJoin.get("categorie"), "postCondition"),
            cb.between(conditionJoin.get("dateEcheance"), start, end)
        );

        // Manually compute dateLancement + numberSemaineApresLancement
        Expression<LocalDate> preEffectiveDate = cb.function(
            "DATE_ADD", LocalDate.class,
            conditionJoin.get("dateLancement"),
            cb.concat(conditionJoin.get("numberSemaineApresLancement"), " WEEK")
        );

        Predicate preCondition = cb.and(
            cb.equal(conditionJoin.get("categorie"), "preCondition"),
            cb.between(preEffectiveDate, start, end)
        );

        predicates.add(cb.or(postCondition, preCondition));
    }

    cq.where(predicates.toArray(new Predicate[0]));
    cq.select(risqueRoot).distinct(true);

    List<Risque> result = entityManager.createQuery(cq).getResultList();

    // Map manually to DTOs
    return buildValidateurReportDTOs(result);
}
