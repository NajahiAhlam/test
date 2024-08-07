    @Query("SELECT "
            + "COUNT(CASE WHEN s.phaseProjet IN ('Opportunité', 'Initialisation') THEN 1 END) AS NonLances, "
            + "COUNT(CASE WHEN s.phaseProjet = 'Cadrage' THEN 1 END) AS EnCadrage, "
            + "COUNT(CASE WHEN s.phaseProjet = 'Réalisation' THEN 1 END) AS EnRealisation, "
            + "COUNT(CASE WHEN s.phaseProjet = 'Préclôture' THEN 1 END) AS Preclotures, "
            + "COUNT(CASE WHEN s.phaseProjet = 'Clôturé' THEN 1 END) AS Clotures, "
            + "COUNT(CASE WHEN s.phaseProjet IN ('Suspendu', 'Remplacé', 'Abandonné') THEN 1 END) AS Abandonnees "
            + "FROM Sciforma s "
            + "WHERE s.annee = :year AND s.programme = :programme AND s.lead = :lead")
    List<Object[]> getCountByPhase(@Param("year") Long year, @Param("programme") String programme,
                                   @Param("lead") String lead);

    can you update this to take on consideration that the parametre can be onr of them null programme and lead
    and give me the front here an exemple of how i work
        this.analyticsService.getAggregatedData1(this.selectedYear, this.selectedProgramme, this.selectedLead, this.selectedEnjeux).subscribe(data => {
        this.aggregatedData = data;
      });
       <h4>{{ aggregatedData?.countInitiativeTrueRTb || 0 }}</h4>
