@Query("SELECT "
     + "COUNT(CASE WHEN p.typeInvestissement.nom = 'RTB' AND p.isInitiative = false THEN 1 ELSE NULL END) AS countProjetsTransverse, "
     + "SUM(CASE WHEN p.isInitiative = false THEN b.intern + b.extern ELSE 0 END) AS sumTotalETP, "
     + "COUNT(CASE WHEN p.isInitiative = false AND p.phase <> 'Opportunité' THEN 1 ELSE NULL END) "
     + "  + COUNT(CASE WHEN p.isInitiative = true "
     + "                 AND p.phase <> 'Opportunité' "
     + "                 AND f.dateOuverture >= CURRENT_DATE "
     + "                 AND f.dateCloture <= CURRENT_DATE THEN 1 ELSE NULL END) "
     + "AS numerator, "
     + "COUNT(CASE WHEN p.isInitiative = false "
     + "             OR (p.isInitiative = true "
     + "                 AND f.dateOuverture >= CURRENT_DATE "
     + "                 AND f.dateCloture <= CURRENT_DATE) THEN 1 ELSE NULL END) "
     + "AS denominator "
     + "FROM Projet p "
     + "JOIN p.fdr f "
     + "JOIN p.leadPrincipal l "
     + "JOIN l.budget b "
     + "WHERE (:programme IS NULL OR p.programme.nom = :programme) "
     + "AND (:lead IS NULL OR EXISTS (SELECT l FROM p.leadPrincipal l WHERE l.intitule = :lead)) "
     + "AND (:enjeux IS NULL OR EXISTS (SELECT e FROM p.enjeux e WHERE e.nom = :enjeux))")
Map<String, Object> getAggregatedMetrics(@Param("programme") String programme,
                                         @Param("lead") String lead,
                                         @Param("enjeux") String enjeux);
------------
@Service
public class ProjetService {

    @Autowired
    private ProjetRepository projetRepository;

    public Map<String, Object> getAggregatedMetrics(String programme, String lead, String enjeux) {
        Map<String, Object> result = projetRepository.getAggregatedMetrics(programme, lead, enjeux);

        Long countProjetsTransverse = ((Number) result.get("countProjetsTransverse")).longValue();
        Long sumTotalETP = ((Number) result.get("sumTotalETP")).longValue();
        Long numerator = ((Number) result.get("numerator")).longValue();
        Long denominator = ((Number) result.get("denominator")).longValue();

        Double tauxAnnuelLancement = (denominator == 0) ? null : numerator.doubleValue() / denominator.doubleValue();

        Map<String, Object> metrics = new HashMap<>();
        metrics.put("countProjetsTransverse", countProjetsTransverse);
        metrics.put("sumTotalETP", sumTotalETP);
        metrics.put("numerator", numerator);
        metrics.put("denominator", denominator);
        metrics.put("tauxAnnuelLancement", tauxAnnuelLancement);

        return metrics;
    }
}


-------------------------------------------------------------


@Query("SELECT COUNT(CASE WHEN p.typeInvestissement = 'RTB' AND p.isInitiative = false THEN 1 ELSE NULL END) AS countProjetsTransverse, "
        + "SUM(CASE WHEN p.isInitiative = false THEN b.intern ELSE 0 END) + SUM(CASE WHEN p.isInitiative = false THEN b.extern ELSE 0 END) AS sumTotalETP, "
        + "COUNT(CASE WHEN p.isInitiative = false AND p.phase <> 'Opportunité' THEN 1 ELSE NULL END) "
        + " + COUNT(CASE WHEN p.isInitiative = true AND p.phase <> 'Opportunité' AND f.dateOuverture >= CURRENT_DATE AND f.dateCloture <= CURRENT_DATE THEN 1 ELSE NULL END) AS numerator, "
        + "COUNT(CASE WHEN p.isInitiative = false OR (p.isInitiative = true AND f.dateOuverture >= CURRENT_DATE AND f.dateCloture <= CURRENT_DATE) THEN 1 ELSE NULL END) AS denominator "
        + "FROM Projet p "
        + "LEFT JOIN p.fdr f "
        + "LEFT JOIN p.budgetInitial b "
        + "WHERE (:programme IS NULL OR p.programme.nom = :programme) "
        + "AND (:lead IS NULL OR p.leadPrincipal.intitule = :lead) "
        + "AND (:enjeux IS NULL OR EXISTS (SELECT e FROM p.enjeux e WHERE e.nom = :enjeux))")
List<Object[]> getAggregatedMetrics(@Param("programme") String programme, @Param("lead") String lead, @Param("enjeux") String enjeux);



