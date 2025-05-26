SELECT 
  u.id AS validateur_id,
  u.nom AS validateur_nom,
  u.prenom AS validateur_prenom,
  
  r.id AS risque_id,
  r.name AS risque_name,
  
  ri.id AS risque_instance_id,
  ri.name AS risque_instance_name,
  ri.contexte,
  ri.niveauRisqueResiduel,
  ri.NiveauIntrinseque,
  ri.typeValidation,
  ri.createdAt AS ri_createdAt,
  ri.updatedAt AS ri_updatedAt,
  ri.valider,
  ri.initier,
  ri.dateValidation,
  ri.comment,
  ri.autreRisque,
  
  dq.id AS demande_id,
  dq.nomFiliale,
  dq.objetDemande,
  dq.type AS demande_type,
  dq.description AS demande_description,
  dq.dateLancement AS demande_dateLancement,
  dq.dateDemande,
  dq.dateKickOff,
  dq.dateQualification,
  dq.status,
  dq.kickOffDecision,
  dq.codeProduit,
  dq.nomProduit,
  dq.descriptionProduit,
  dq.datePreCnp,
  dq.dateCnp,
  dq.typeCnp,
  
  c.id AS condition_id,
  c.detail AS condition_detail,
  c.categorie AS condition_categorie,
  c.assigne_id AS condition_assigne_id,
  c.numberSemaineApresLancement,
  c.dateLancement AS condition_dateLancement,
  c.dateEcheance,
  c.dateColoture,
  c.etat
  
FROM risque r
JOIN user u ON r.validateur_id = u.id
JOIN risque_instance ri ON ri.risque_id = r.id
LEFT JOIN demande_qualification dq ON ri.demande_qualification_id = dq.id
LEFT JOIN conditions c ON c.risque_instance_id = ri.id

WHERE 
  (
    (:startDate IS NULL OR :endDate IS NULL)
    OR
    (
      (c.categorie = 'postCondition' AND c.dateEcheance BETWEEN :startDate AND :endDate)
      OR
      (c.categorie = 'preCondition' AND DATE_ADD(c.dateLancement, INTERVAL c.numberSemaineApresLancement WEEK) BETWEEN :startDate AND :endDate)
    )
  )
ORDER BY u.id, r.id, ri.id, c.id;
