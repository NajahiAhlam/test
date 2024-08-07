   @Query("SELECT "
            + "COUNT(CASE WHEN s.phaseProjet IN ('Opportunité', 'Initialisation') THEN 1 END) AS NonLances, "
            + "COUNT(CASE WHEN s.phaseProjet = 'Cadrage' THEN 1 END) AS EnCadrage, "
            + "COUNT(CASE WHEN s.phaseProjet = 'Réalisation' THEN 1 END) AS EnRealisation, "
            + "COUNT(CASE WHEN s.phaseProjet = 'Préclôture' THEN 1 END) AS Preclotures, "
            + "COUNT(CASE WHEN s.phaseProjet = 'Clôturé' THEN 1 END) AS Clotures, "
            + "COUNT(CASE WHEN s.phaseProjet IN ('Suspendu', 'Remplacé', 'Abandonné') THEN 1 END) AS Abandonnees "
            + "FROM Sciforma s "
            + "WHERE (:year IS NULL OR s.annee = :year) "
            + "AND (:programme IS NULL OR s.programme = :programme) "
            + "AND (:lead IS NULL OR s.lead = :lead)")
    List<Object[]> getCountByPhase(@Param("year") Long year, 
                                   @Param("programme") String programme, 
                                   @Param("lead") String lead);
    
    public List<Object[]> getCountByPhase(Long year, String programme, String lead) {
        return sciformaRepository.getCountByPhase(year, programme, lead);
    }
    
    @GetMapping("/countByPhase")
    public List<Object[]> getCountByPhase(
            @RequestParam(value = "year", required = false) Long year,
            @RequestParam(value = "programme", required = false) String programme,
            @RequestParam(value = "lead", required = false) String lead) {
        return sciformaServiceImp.getCountByPhase(year, programme, lead);
    }
    aggregatedData: any;
    this.analyticsService.getAggregatedData1(this.selectedYear, this.selectedProgramme, this.selectedLead).subscribe(data => {
      this.aggregatedData = data;
    });
    <h4>{{ aggregatedData?.NonLances || 0 }}</h4>
<h4>{{ aggregatedData?.EnCadrage || 0 }}</h4>
<h4>{{ aggregatedData?.EnRealisation || 0 }}</h4>
<h4>{{ aggregatedData?.Preclotures || 0 }}</h4>
<h4>{{ aggregatedData?.Clotures || 0 }}</h4>
<h4>{{ aggregatedData?.Abandonnees || 0 }}</h4>
