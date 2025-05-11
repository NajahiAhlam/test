@Transactional
public RisqueInstanceDTO createRisqueInstance(RisqueInstanceDTO dto, Long demandeId) {
    for (ConditionsDTO conditionDTO : dto.getConditions()) {
        // Fetch user by email (assigne)
        Optional<User> assigneeUser = userRepository.findByEmail(conditionDTO.getAssigne());
        if (assigneeUser.isEmpty()) {
            // Handle the case where the user is not found
            throw new RuntimeException("User with email " + conditionDTO.getAssigne() + " not found");
        }

        // Create and save the new Conditions object
        Conditions condition = new Conditions();
        condition.setCategorie(conditionDTO.getCategorie());
        condition.setDateEcheance(conditionDTO.getDateEcheance());
        condition.setDateLancement(conditionDTO.getDateLancement());
        condition.setDetail(conditionDTO.getDetail());
        condition.setEtat(conditionDTO.getEtat());
        condition.setNumberSemaineApresLancement(conditionDTO.getNumberSemaineApresLancement());

        // Set the User as the assignee
        condition.setAssigne(assigneeUser.get());

        // Save the condition entity first
        conditionsRepository.save(condition);
        conditionDTO.setId(condition.getId());
    }

    // Create and save the RisqueInstance object
    RisqueInstance risqueInstance = RisqueInstanceMapper.toEntity(dto, userRepository);
    
    // Save the RisqueInstance
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
