private List<ValidateurReportDTO> buildValidateurReportDTOs(List<Risque> risques) {
    Map<User, ValidateurReportDTO> reportMap = new HashMap<>();

    for (Risque risque : risques) {
        User validateur = risque.getValidateur();
        ValidateurReportDTO report = reportMap.computeIfAbsent(validateur, v -> {
            ValidateurReportDTO dto = new ValidateurReportDTO();
            dto.setNom(v.getNom());
            dto.setPrenom(v.getPrenom());
            dto.setRisques(new ArrayList<>());
            return dto;
        });

        RisqueDTO risqueDTO = new RisqueDTO();
        risqueDTO.setName(risque.getName());
        risqueDTO.setInstances(new ArrayList<>());

        for (RisqueInstance instance : risque.getRisqueInstances()) {
            RisqueInstanceDTO instanceDTO = new RisqueInstanceDTO();
            instanceDTO.setId(instance.getId());
            instanceDTO.setName(instance.getName());

            // Demande info
            DemandeQualification demande = instance.getDemandeQualification();
            if (demande != null) {
                DemandeQualificationDTO dq = new DemandeQualificationDTO();
                dq.setId(demande.getId());
                dq.setNomFiliale(demande.getNomFiliale());
                instanceDTO.setDemande(dq);
            }

            // Conditions filtering
            List<ConditionDTO> conditionDTOs = instance.getConditions().stream()
                .filter(c -> {
                    if (start == null || end == null) return true;
                    if ("postCondition".equals(c.getCategorie())) {
                        return isBetween(c.getDateEcheance(), start, end);
                    } else if ("preCondition".equals(c.getCategorie())) {
                        return isBetween(
                            c.getDateLancement().plusWeeks(c.getNumberSemaineApresLancement()),
                            start, end
                        );
                    }
                    return false;
                })
                .map(this::mapConditionToDTO)
                .collect(Collectors.toList());

            instanceDTO.setConditions(conditionDTOs);
            risqueDTO.getInstances().add(instanceDTO);
        }

        report.getRisques().add(risqueDTO);
    }

    return new ArrayList<>(reportMap.values());
}

private boolean isBetween(LocalDate date, LocalDate start, LocalDate end) {
    return (date != null && (date.isEqual(start) || date.isAfter(start)) && date.isBefore(end.plusDays(1)));
}
    @PersistenceContext
    private EntityManager entityManager;
