  public RisqueInstanceDTO createRisqueInstance(RisqueInstanceDTO dto) {
        // Map the DTO to the entity using the static mapper
        RisqueInstance risqueInstance = RisqueInstanceMapper.toEntity(dto, userRepository);

        // Persist the entity using the repository
        RisqueInstance savedRisqueInstance = risqueInstanceRepository.save(risqueInstance);

        // Map the saved entity back to DTO to return
        return risqueInstanceMapper.toDTO(savedRisqueInstance);
    }
