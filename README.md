public interface ReportRepository extends JpaRepository<Risque, Long> {

    @Query(value = 
        "SELECT " +
        " u.user_id AS validateur_id, " +
        " u.nom AS validateur_nom, " +
        " u.prenom AS validateur_prenom, " +
        " r.id AS risque_id, " +
        " r.name AS risque_name, " +
        " ri.id AS risque_instance_id, " +
        " ri.name AS risque_instance_name, " +
        " ri.contexte, " +
        " ri.type_validation, " +
        " ri.created_at AS ri_createdAt, " +
        " ri.updated_at AS ri_updatedAt, " +
        " ri.date_validation, " +
        " ri.comment, " +
        " dq.id AS demande_id, " +
        " dq.nom_filiale, " +
        " dq.objet_demande, " +
        " dq.type AS demande_type, " +
        " dq.description AS demande_description, " +
        " dq.date_lancement AS demande_dateLancement, " +
        " dq.date_demande, " +
        " dq.date_kick_off, " +
        " dq.status, " +
        " c.id AS condition_id, " +
        " c.detail AS condition_detail, " +
        " c.categorie AS condition_categorie, " +
        " c.assigne_user_id AS condition_assigne_id, " +
        " c.number_semaine_apres_lancement, " +
        " c.date_lancement AS condition_dateLancement, " +
        " c.date_echeance, " +
        " c.date_coloture, " +
        " c.etat " +
        "FROM risque r " +
        "JOIN users u ON r.validateur_user_id = u.user_id " +
        "JOIN risque_instance ri ON ri.risque_id = r.id " +
        "LEFT JOIN risque_instance_demande_qualification ridq ON ridq.risque_instance_id = ri.id " +
        "LEFT JOIN demande_qualification dq ON dq.id = ridq.demande_qualification_id " +
        "LEFT JOIN risque_instance_conditions ric ON ric.risque_instance_id = ri.id " +
        "LEFT JOIN conditions c ON c.id = ric.conditions_id AND ( " +
        "(:startDate IS NULL OR :endDate IS NULL) OR ( " +
        "(c.categorie = 'postCondition' AND c.date_echeance BETWEEN :startDate AND :endDate) OR " +
        "(c.categorie = 'preCondition' AND DATE_ADD(c.date_lancement, INTERVAL c.number_semaine_apres_lancement WEEK) BETWEEN :startDate AND :endDate) " +
        ") " +
        ") " +
        "ORDER BY u.user_id, r.id, ri.id, c.id", 
        nativeQuery = true)
    List<Object[]> findReporting(@Param("startDate") LocalDate startDate, @Param("endDate") LocalDate endDate);
}
SELECT 
  u.user_id AS validateur_id,
  u.nom AS validateur_nom,
  u.prenom AS validateur_prenom,
  
  r.id AS risque_id,
  r.name AS risque_name,
  
  ri.id AS risque_instance_id,
  ri.name AS risque_instance_name,
  ri.contexte,
  ri.type_validation,
  ri.created_at AS ri_createdAt,
  ri.updated_at AS ri_updatedAt,
  ri.date_validation,
  ri.comment,
  
  dq.id AS demande_id,
  dq.nom_filiale,
  dq.objet_demande,
  dq.type AS demande_type,
  dq.description AS demande_description,
  dq.date_lancement AS demande_dateLancement,
  dq.date_demande,
  dq.date_kick_off,
  dq.status,
  
  c.id AS condition_id,
  c.detail AS condition_detail,
  c.categorie AS condition_categorie,
  c.assigne_user_id AS condition_assigne_id,
  c.number_semaine_apres_lancement,
  c.date_lancement AS condition_dateLancement,
  c.date_echeance,
  c.date_coloture,
  c.etat
  
FROM risque r
JOIN users u ON r.validateur_user_id = u.user_id
JOIN risque_instance ri ON ri.risque_id = r.id

LEFT JOIN demande_qualification_risques ridq ON ridq.risques_id = ri.id
LEFT JOIN demande_qualification dq ON dq.id = ridq.demande_qualification_id

LEFT JOIN risque_instance_conditions ric ON ric.risque_instance_id = ri.id
LEFT JOIN conditions c ON c.id = ric.conditions_id
LEFT JOIN conditions c ON c.risque_instance_id = ri.id
  AND (
    ('2025-05-12' IS NULL OR '2025-05-12' IS NULL)
    OR
    (
      (c.categorie = 'postCondition' AND c.date_echeance BETWEEN '2025-05-12' AND '2025-05-30')
      OR
      (c.categorie = 'preCondition' AND DATE_ADD(c.date_lancement, INTERVAL c.number_semaine_apres_lancement WEEK) BETWEEN '2025-05-12' AND '2025-05-30')
    )
  )




ORDER BY u.user_id, r.id, ri.id, c.id;

