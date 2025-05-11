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
            condition.setEtat("en cours");
            condition.setNumberSemaineApresLancement(conditionDTO.getNumberSemaineApresLancement());
            // Set the assignee for the condition
            condition.setAssigne(assigneeUser.get());

            // Save the condition first
            conditionsRepository.save(condition);
            try {
                risqueService.joinCommentCondition(condition.getId(), conditionDTO.getCommentCond());
            } catch (PermissionException e) {
                throw new RuntimeException(e);
            }
            conditionsList.add(condition);  // Add the condition to the list

            conditionDTO.setId(condition.getId());
        }

        List<ZoneRisqueInstance> zoneRisqueInstances = new ArrayList<>();;
        for (ZoneRisqueInstanceDTO zoneRisqueInstanceDTO : dto.getZoneRisques()) {
            ZoneRisqueInstance zoneRisqueInstance = new ZoneRisqueInstance();
            zoneRisqueInstance.setName(zoneRisqueInstanceDTO.getName());

            ZoneRisque zoneRisque = zoneRisqueRepository.findById(zoneRisqueInstanceDTO.getZoneRisqueId())
                    .orElseThrow(() -> new IllegalArgumentException("Aucune zone risque avec id = " + zoneRisqueInstanceDTO.getZoneRisqueId()));
            zoneRisqueInstance.setZoneRisque(zoneRisque);

            List<AnalyseInstance> analyseRisques = new ArrayList<>();
            for (AnalyseReponseDTO analyseDTO : zoneRisqueInstanceDTO.getAnalyseRisques()) {
                AnalyseInstance analyseRisque = new AnalyseInstance();
                analyseRisque.setReponse(analyseDTO.getReponse());

                 AnalyseRisque analyseRis = analyseRisqueRepository.findById(analyseDTO.getAnalyseRisqueId())
                        .orElseThrow(() -> new IllegalArgumentException("Aucune analyse risque avec id = " + analyseDTO.getAnalyseRisqueId()));
                analyseRisque.setAnalyseRisque(analyseRis);
                analyseRisques.add(analyseRisque);

                analyseInstanceRepository.save(analyseRisque);
            }
            zoneRisqueInstance.setAnalyseInstances(analyseRisques);
            zoneRisqueInstanceRepository.save(zoneRisqueInstance);
            zoneRisqueInstances.add(zoneRisqueInstance);
        }

        // Now, create and save the RisqueInstance
        RisqueInstance risqueInstance = RisqueInstanceMapper.toEntity(dto, userRepository);
        risqueInstance.setConditions(conditionsList); // Set the conditions to the RisqueInstance


        if (dto.getConditions() == null || dto.getConditions().isEmpty()) {
            risqueInstance.setTypeValidation("validation sans condition");
        } else {
            risqueInstance.setTypeValidation("validation avec condition");
        }
        Risque risque = risqueRepository.findById(dto.getRisqueId())
                .orElseThrow(() -> new IllegalArgumentException("Aucune risque avec id = " + dto.getRisqueId()));
        risqueInstance.setRisque(risque);
        risqueInstance.setInitier(true);
        risqueInstance.setZoneRisques(zoneRisqueInstances);
        risqueInstance.setNiveauIntrinseque(dto.getNiveauIntrinseque());
        risqueInstance.setNiveauRisqueResiduel(dto.getNiveauRisqueResiduel());
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
hey chat this is my create methode want if user create risque the button of ajout risque in front not showing again how can i do it chaque user ajout risque that he validateur of it
