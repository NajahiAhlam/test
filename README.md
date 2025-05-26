LEFT JOIN risque_instance_conditions ric ON ric.risque_instance_id = ri.id
LEFT JOIN conditions c ON c.id = ric.conditions_id
