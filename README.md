@Query(value = 
    "SELECT c.id, c.assigne_user_id, u.email, COUNT(c.id), c.date_lancement, c.number_semaine_apres_lancement, c.date_echeance, c.categorie, c.etat " +
    "FROM conditions c " +
    "JOIN users u ON c.assigne_user_id = u.user_id " +
    "WHERE (:startDate IS NULL OR :endDate IS NULL OR " +
    "  (c.categorie = 'poste condition' AND " +
    "   (c.date_lancement + INTERVAL '1 week' * c.number_semaine_apres_lancement) BETWEEN :startDate AND :endDate) " +
    "  OR " +
    "  (c.categorie = 'précondition' AND c.date_echeance BETWEEN :startDate AND :endDate) " +
    ") " +
    "GROUP BY c.id, c.assigne_user_id, u.email, c.date_lancement, c.number_semaine_apres_lancement, c.date_echeance, c.categorie, c.etat",
    nativeQuery = true)
List<Object[]> findFilteredConditions(
    @Param("startDate") LocalDate startDate, 
    @Param("endDate") LocalDate endDate);
