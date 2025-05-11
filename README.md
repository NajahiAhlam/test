@Transactional
public RisqueInstanceDTO createRisqueInstance(RisqueInstanceDTO dto, Long demandeId) {
    List<Conditions> conditionsList = new ArrayList<>();
    for (ConditionsDTO conditionDTO : dto.getConditions()) {
        // Fetch user by email (assigne)
        Optional<User> assigneeUser = userRepository.findByEmail(conditionDTO.getAssigne());
        if (!assigneeUser.isPresent()) {
            // Handle the case where the user is not found
            throw new RuntimeException("User with email " + conditionDTO.getAssigne() + " not found");
        }

        // Create the Conditions object
        Conditions condition = new Conditions();
        condition.setCategorie(conditionDTO.getCategorie());
        condition.setDateEcheance(conditionDTO.getDateEcheance());
        condition.setDateLancement(conditionDTO.getDateLancement());
        condition.setDetail(conditionDTO.getDetail());
        condition.setEtat(conditionDTO.getEtat());
        condition.setNumberSemaineApresLancement(conditionDTO.getNumberSemaineApresLancement());

        // Set the assignee for the condition
        condition.setAssigne(assigneeUser.get());

        // Save the condition first
        conditionsRepository.save(condition);
        conditionsList.add(condition);  // Add the condition to the list

        conditionDTO.setId(condition.getId());
    }

    // Now, create and save the RisqueInstance
    RisqueInstance risqueInstance = RisqueInstanceMapper.toEntity(dto, userRepository);
    risqueInstance.setConditions(conditionsList); // Set the conditions to the RisqueInstance
    RisqueInstance savedRisqueInstance = risqueInstanceRepository.save(risqueInstance);

    // Find and update the DemandeQualification
    DemandeQualification demande = demandeQualificationRepository.findById(demandeId)
            .orElseThrow(() -> new IllegalArgumentException("Aucune demande avec id = " + demandeId));

    // Add the new RisqueInstance to the DemandeQualification
    List<RisqueInstance> risqueInstances = Optional.ofNullable(demande.getRisques()).orElse(new ArrayList<>());
    risqueInstances.add(savedRisqueInstance);
    demande.setRisques(risqueInstances);

    // Save the updated DemandeQualification
    demandeQualificationRepository.save(demande);

    // Return the saved RisqueInstance as a DTO
    return RisqueInstanceMapper.toDTO(savedRisqueInstance);
}
