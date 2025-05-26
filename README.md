LEFT JOIN risque_instance_demande_qualification ridq ON ridq.risque_instance_id = ri.id
LEFT JOIN demande_qualification dq ON dq.id = ridq.demande_qualification_id
