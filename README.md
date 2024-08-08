public Map<String, Object> getAggregatedMetrics(Long year, String programme, String lead) {
    Map<String, Object> result = projetRepository.getAggregatedMetrics(year, programme, lead);

    // Safely retrieve values and handle nulls
    Long countProjetsTransverse = result.get("countProjetsTransverse") != null ?
            ((Number) result.get("countProjetsTransverse")).longValue() : 0L;
    Long sumIntern = result.get("sumIntern") != null ?
            ((Number) result.get("sumIntern")).longValue() : 0L;
    Long sumExtern = result.get("sumExtern") != null ?
            ((Number) result.get("sumExtern")).longValue() : 0L;
    Long numerator = result.get("numerator") != null ?
            ((Number) result.get("numerator")).longValue() : 0L;
    Long denominator = result.get("denominator") != null ?
            ((Number) result.get("denominator")).longValue() : 0L;

    double tauxAnnuelLancement = (denominator != 0) ?
            (double) numerator / denominator * 100 : 0;

    double percentageIntern = (sumIntern + sumExtern > 0) ?
            (double) sumIntern / (sumIntern + sumExtern) * 100 : 0;

    // If you intend to use percentageIntern to calculate sumTotalRtp, ensure it is handled correctly
    double sumTotalRtp = (percentageIntern > 0) ?


    
            (double) countProjetsTransverse / percentageIntern * 100 : 0;

    // Prepare metrics map
    Map<String, Object> metrics = new HashMap<>();
    metrics.put("countProjetsTransverse", countProjetsTransverse);
    metrics.put("sumTotalETP", percentageIntern);
    metrics.put("numerator", numerator);
    metrics.put("denominator", denominator);
    metrics.put("tauxAnnuelLancement", tauxAnnuelLancement);

    return metrics;
}
@Query("SELECT COUNT(CASE WHEN p.typeInvestissement = 'RTB' AND p.isInitiative = false THEN 1 ELSE NULL END) AS countProjetsTransverse, "
        + "SUM(CASE WHEN p.isInitiative = false THEN l.budgetInitial.intern ELSE 0 END) AS sumIntern, "
        + "SUM(CASE WHEN p.isInitiative = false THEN l.budgetInitial.extern ELSE 0 END) AS sumExtern, "
        + "COUNT(CASE WHEN p.isInitiative = false AND p.phase <> 'Opportunité' THEN 1 ELSE NULL END) "
        + " + COUNT(CASE WHEN p.isInitiative = true AND p.phase <> 'Opportunité' AND f.dateOuverture >= CURRENT_DATE AND f.dateCloture <= CURRENT_DATE THEN 1 ELSE NULL END) AS numerator, "
        + "COUNT(CASE WHEN p.isInitiative = false OR (p.isInitiative = true AND f.dateOuverture >= CURRENT_DATE AND f.dateCloture <= CURRENT_DATE) THEN 1 ELSE NULL END) AS denominator "
        + "FROM Projet p "
        + "LEFT JOIN p.fdr f "
        + "LEFT JOIN p.leadPrincipal l "
        + "WHERE (:programme IS NULL OR p.programme.nom = :programme) "
        + "AND f.annee = :year "
        + "AND (:lead IS NULL OR p.leadPrincipal.intitule = :lead)")
Map<String, Object> getAggregatedMetrics(@Param("year") Long year, @Param("programme") String programme, @Param("lead") String lead);
