@Query("SELECT COUNT(CASE WHEN p.typeInvestissement = 'RTB' AND p.isInitiative = false THEN 1 ELSE NULL END) AS countProjetsTransverse, "
            +"SUM(CASE WHEN p.isInitiative = false THEN l.budgetInitial.intern ELSE 0 END) / SUM(CASE WHEN p.isInitiative = false THEN l.budgetInitial.intern ELSE 0 END) + SUM(CASE WHEN p.isInitiative = false THEN l.budgetInitial.extern ELSE 0 END) AS sumTotalETP, "
            + "COUNT(CASE WHEN p.isInitiative = false AND p.phase <> 'Opportunité' THEN 1 ELSE NULL END) "
            + " + COUNT(CASE WHEN p.isInitiative = true AND p.phase <> 'Opportunité' AND f.dateOuverture >= CURRENT_DATE AND f.dateCloture <= CURRENT_DATE THEN 1 ELSE NULL END) AS numerator, "
            + "COUNT(CASE WHEN p.isInitiative = false OR (p.isInitiative = true AND f.dateOuverture >= CURRENT_DATE AND f.dateCloture <= CURRENT_DATE) THEN 1 ELSE NULL END) AS denominator "
            + "FROM Projet p "
            + "LEFT JOIN p.fdr f "
            + "LEFT JOIN p.leadPrincipal l "
            + "WHERE (:programme IS NULL OR p.programme.nom = :programme) "
            +"  AND f.annee = :year "
            + "AND (:lead IS NULL OR p.leadPrincipal.intitule = :lead) ")
    Map<String, Object> getAggregatedMetrics(@Param("year") Long year,@Param("programme") String programme, @Param("lead") String lead); 
    i have the error cause i have this line  +"SUM(CASE WHEN p.isInitiative = false THEN l.budgetInitial.intern ELSE 0 END) / SUM(CASE WHEN p.isInitiative = false THEN l.budgetInitial.intern ELSE 0 END) + SUM(CASE WHEN p.isInitiative = false THEN l.budgetInitial.extern ELSE 0 END) AS sumTotalETP, " when this SUM(CASE WHEN p.isInitiative = false THEN l.budgetInitial.intern ELSE 0 END) + SUM(CASE WHEN p.isInitiative = false THEN l.budgetInitial.extern  get 0 error of cdevise par 0
