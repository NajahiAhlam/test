public Map<String, Object> getAggregatedMetrics(Long year, String programme, String lead) {
    Map<String, Object> result = projetRepository.getSumInternAndExtern(year, programme, lead);

    Long sumIntern = ((Number) result.get("sumIntern")).longValue();
    Long sumExtern = ((Number) result.get("sumExtern")).longValue();

    // Calculate the percentage of intern over the total (intern + extern)
    double percentageIntern = (sumIntern + sumExtern > 0) 
        ? (double) sumIntern / (sumIntern + sumExtern) * 100 
        : 0;

    Map<String, Object> metrics = new HashMap<>();
    metrics.put("sumIntern", sumIntern);
    metrics.put("sumExtern", sumExtern);
    metrics.put("percentageIntern", percentageIntern);

    return metrics;
}
