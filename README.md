public Map<String, Object> getAggregatedMetrics(Long year, String programme, String lead) {
    Map<String, Object> result = projetRepository.getAggregatedMetrics(year, programme, lead);

    // Safely retrieve values and handle nulls
    Long countProjetsTransverse = result.containsKey("countProjetsTransverse") ? 
                                   ((Number) result.get("countProjetsTransverse")).longValue() : 0L;
    Long sumTotalETP = result.containsKey("sumTotalETP") ? 
                       ((Number) result.get("sumTotalETP")).longValue() : 0L;
    Long numerator = result.containsKey("numerator") ? 
                     ((Number) result.get("numerator")).longValue() : 0L;
    Long denominator = result.containsKey("denominator") ? 
                       ((Number) result.get("denominator")).longValue() : 0L;

    // Calculate tauxAnnuelLancement
    double tauxAnnuelLancement = (denominator != 0) ? 
                                  (double) numerator / denominator * 100 : 0;

    // Calculate sumTotalRtp as percentage
    double sumTotalRtp = (sumTotalETP > 0) ? 
                          (double) countProjetsTransverse / sumTotalETP * 100 : 0;

    // Prepare metrics map
    Map<String, Object> metrics = new HashMap<>();
    metrics.put("countProjetsTransverse", countProjetsTransverse);
    metrics.put("sumTotalETP", sumTotalETP);
    metrics.put("numerator", numerator);
    metrics.put("denominator", denominator);
    metrics.put("tauxAnnuelLancement", tauxAnnuelLancement);
    metrics.put("sumTotalRtp", sumTotalRtp);

    return metrics;
}
