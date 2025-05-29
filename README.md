    @Query(value = "SELECT  c.id, c.assigne_user_id, u.email, COUNT(c.id) , c.date_lancement, c.number_semaine_apres_lancement, c.date_echeance, c.categorie, c.etat " +
            "FROM conditions c " +
            "JOIN users u ON c.assigne_user_id = u.user_id " +
            "WHERE (:startDate IS NULL OR " +
            "(c.categorie = 'poste condition' AND (c.date_lancement + INTERVAL '1 week' * c.number_semaine_apres_lancement) BETWEEN :startDate AND :endDate)) " +
            "OR (:startDate IS NULL OR " +
            "(c.categorie = 'précondition' AND c.date_echeance BETWEEN :startDate AND :endDate)) " +
            "GROUP BY c.id, c.assigne_user_id, u.email",
            nativeQuery = true)
    List<Object[]> findFilteredConditions(@Param("startDate") LocalDate startDate, @Param("endDate") LocalDate endDate);
the query doesnt worked i have in condition in dateEchenece 2025-05-05 and other in sum of c.date_lancement + INTERVAL '1 week' * c.number_semaine_apres_lancement i have 2025-05-25 and they get them t me with filter in mounth 4 2025 cna you update the query want to get condition where condition.categorie = 'précondition'
to get it if his dateEcheance betwen param and if categorie =  'poste condition' get if dateLancement + nbr semaine get date and thid date between param
