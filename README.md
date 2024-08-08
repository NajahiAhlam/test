i want to generete me a query with jpql that this get

	Nombre de projets Transverse : les projets INVEST avec Type de Budget « RTB » explain : i have in entite projet TypeInvisstissemt relation so i want to get the nombre of projet that have typeInvistissement.nom=="RTB" and projet.isInitiative = false
	Taux annuel de Lancement : (Nombre de Projet avec « Initiative Année en cours » activé + Status différent de « Opportunité ») / Nombre de Projet avec « Initiative Année en cours » activé explain: in projet i have relation Fdr where i have attribut Long annee and in projet I have attribut boolean isInitiative so in (Nombre de Projet avec « Initiative Année en cours » activé + Status différent de « Opportunité ») i want to get nombre of all projet that have isInitiative false + nombre of projet that have isInitiative true that they fdr.dateOuverture is >= date now and fdr.dateClotutre is w date now all of this exclue the projet that have projet.phase différent de « Opportunité ») /  Nombre de Projet avec « Initiative Année en cours » activé  want to get nombre of all projet that have isInitiative false + nombre of projet that have isInitiative true that they fdr.dateOuverture is >= date now and fdr.dateClotutre is w date now 
	%ETP Internes/ Total effectif : Le % du Nombre Total des  INTERN / Nombre total (INTERN+EXTERN) explain: i have in projet entity reltion LeadPrincipal type Lead and in Lead Entity i have relation with budget so in budget i have attributs type Long Extern, Intern projet.isInitiative = false


i give you an exemple of query that i have :   @Query("SELECT "
            + "COUNT(CASE WHEN p.isInitiative = true THEN 1 ELSE NULL END) AS countInitiativeTrue, "
            + "SUM(CASE WHEN p.isInitiative = true THEN b.regie ELSE 0 END) AS sumRegieInitiativeTrue, "
            + "SUM(CASE WHEN p.isInitiative = true THEN b.forfait ELSE 0 END) AS sumForfaitInitiativeTrue, "
            + "SUM(CASE WHEN p.isInitiative = true THEN b.logiciel ELSE 0 END) AS sumLogicielInitiativeTrue, "
            + "SUM(CASE WHEN p.isInitiative = true THEN b.materiel ELSE 0 END) AS sumMaterielInitiativeTrue, "
            + "SUM(CASE WHEN p.isInitiative = true THEN b.sgabs ELSE 0 END) AS sumSgabsInitiativeTrue, "
            + "COUNT(CASE WHEN p.isInitiative = true AND p.typeInvestissement = 'RTb' THEN 1 ELSE NULL END) AS countInitiativeTrueRTb, "
            + "SUM(CASE WHEN p.isInitiative = true AND p.typeInvestissement.nom = 'RTb' THEN b.regie ELSE 0 END) AS sumRegieInitiativeTrueRTb, "
            + "SUM(CASE WHEN p.isInitiative = true AND p.typeInvestissement.nom = 'RTb' THEN b.forfait ELSE 0 END) AS sumForfaitInitiativeTrueRTb, "
            + "SUM(CASE WHEN p.isInitiative = true AND p.typeInvestissement.nom = 'RTb' THEN b.logiciel ELSE 0 END) AS sumLogicielInitiativeTrueRTb, "
            + "SUM(CASE WHEN p.isInitiative = true AND p.typeInvestissement.nom = 'RTb' THEN b.materiel ELSE 0 END) AS sumMaterielInitiativeTrueRTb, "
            + "SUM(CASE WHEN p.isInitiative = true AND p.typeInvestissement.nom = 'RTb' THEN b.sgabs ELSE 0 END) AS sumSgabsInitiativeTrueRTb, "
            + "COUNT(CASE WHEN p.isInitiative = true AND p.refProjet IS NOT NULL THEN 1 ELSE NULL END) AS countRefProjetNotNullInitiativeTrue, "
            + "SUM(CASE WHEN p.isInitiative = true AND p.refProjet IS NOT NULL THEN b.regie ELSE 0 END) AS sumRegieRefProjetNotNullInitiativeTrue, "
            + "SUM(CASE WHEN p.isInitiative = true AND p.refProjet IS NOT NULL THEN b.forfait ELSE 0 END) AS sumForfaitRefProjetNotNullInitiativeTrue, "
            + "SUM(CASE WHEN p.isInitiative = true AND p.refProjet IS NOT NULL THEN b.logiciel ELSE 0 END) AS sumLogicielRefProjetNotNullInitiativeTrue, "
            + "SUM(CASE WHEN p.isInitiative = true AND p.refProjet IS NOT NULL THEN b.materiel ELSE 0 END) AS sumMaterielRefProjetNotNullInitiativeTrue, "
            + "SUM(CASE WHEN p.isInitiative = true AND p.refProjet IS NOT NULL THEN b.sgabs ELSE 0 END) AS sumSgabsRefProjetNotNullInitiativeTrue, "
            + "COUNT(CASE WHEN p.isInitiative = false THEN 1 ELSE NULL END) AS countInitiativeFalse, "
            + "SUM(CASE WHEN p.isInitiative = false THEN b.regie ELSE 0 END) AS sumRegieInitiativeFalse, "
            + "SUM(CASE WHEN p.isInitiative = false THEN b.forfait ELSE 0 END) AS sumForfaitInitiativeFalse, "
            + "SUM(CASE WHEN p.isInitiative = false THEN b.logiciel ELSE 0 END) AS sumLogicielInitiativeFalse, "
            + "SUM(CASE WHEN p.isInitiative = false THEN b.materiel ELSE 0 END) AS sumMaterielInitiativeFalse, "
            + "SUM(CASE WHEN p.isInitiative = false THEN b.sgabs ELSE 0 END) AS sumSgabsInitiativeFalse "
            + "FROM Projet p JOIN p.budgetCtb b "
            + "WHERE p.fdr.annee = :year "
            + "AND (:programme IS NULL OR p.programme.nom = :programme) "
            + "AND (:lead IS NULL OR EXISTS (SELECT l FROM p.leadPrincipal l WHERE l.intitule = :lead)) "
            + "AND (:enjeux IS NULL OR EXISTS (SELECT e FROM p.enjeux e WHERE e.nom = :enjeux))")
    Map<String, Object> getAggregatedCountsAndSums(@Param("year") Long year, @Param("programme") String programme,
                                                   @Param("lead") String lead, @Param("enjeux") String enjeux);


in the query that i want you to generate i want same of this filters that i pass in the parrametre

