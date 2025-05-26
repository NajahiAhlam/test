LEFT JOIN conditions c ON c.risque_instance_id = ri.id
  AND (
    (:startDate IS NULL OR :endDate IS NULL)
    OR
    (
      (c.categorie = 'postCondition' AND c.dateEcheance BETWEEN :startDate AND :endDate)
      OR
      (c.categorie = 'preCondition' AND DATE_ADD(c.dateLancement, INTERVAL c.numberSemaineApresLancement WEEK) BETWEEN :startDate AND :endDate)
    )
  )
