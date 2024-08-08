public Metrics calculateMetrics(String programme, String lead, String enjeux) {
    List<Object[]> results = yourRepository.getAggregatedMetrics(programme, lead, enjeux);

    if (results.isEmpty()) {
        throw new RuntimeException("No data found.");
    }

    Object[] row = results.get(0);
    
    long countProjetsTransverse = ((Number) row[0]).longValue();
    long sumTotalETP = ((Number) row[1]).longValue();
    long numerator = ((Number) row[2]).longValue();
    long denominator = ((Number) row[3]).longValue();

    // Calculate tauxAnnuelLancement
    double tauxAnnuelLancement = denominator > 0 ? (double) numerator / denominator * 100 : 0;

    // Calculate sumTotalRtp as percentage
    double sumTotalRtp = sumTotalETP > 0 ? (double) countProjetsTransverse / sumTotalETP * 100 : 0;

    // Create and return Metrics object or format your results as needed
    Metrics metrics = new Metrics();
    metrics.setTauxAnnuelLancement(tauxAnnuelLancement);
    metrics.setSumTotalRtp(sumTotalRtp);
    return metrics;
}
