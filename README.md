// AnalyseRisqueDTO
public class AnalyseRisqueDTO {
    private Long id;
    private String description;
}

// AnalyseInstanceDTO
public class AnalyseInstanceDTO {
    private Long id;
    private String reponse;
    private String facteur;
    private AnalyseRisqueDTO analyseRisque;
}

// ZoneRisqueDTO
public class ZoneRisqueDTO {
    private Long id;
    private String name;
}

// ZoneRisqueInstanceDTO
public class ZoneRisqueInstanceDTO {
    private Long id;
    private ZoneRisqueDTO zoneRisque;
    private List<AnalyseInstanceDTO> analyseInstances;
}

// RisqueDTO
public class RisqueDTO {
    private Long id;
    private String name;
    private String validateurEmail;
    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;
}

// ConditionsDTO (if needed)
public class ConditionsDTO {
    private Long id;
    private String detail;
    private String categorie;
    private String assigne;
    private Long numberSemaineApresLancement;
    private LocalDate dateLancement;
    private LocalDate dateEcheance;
    private LocalDateTime dateColoture;
    private String etat;
}

// RisqueInstanceDTO
public class RisqueInstanceDTO {
    private Long id;
    private String name;
    private String contexte;
    private String niveauRisqueResiduel;
    private String niveauIntrinseque;
    private String typeValidation;
    private boolean valider;
    private boolean initier;
    private LocalDateTime dateValidation;
    private String comment;
    private String autreRisque;
    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;
    
    private RisqueDTO risque;
    private List<ZoneRisqueInstanceDTO> zoneRisques;
    private List<ConditionsDTO> conditions;
}


public class RisqueInstanceMapper {

    public static RisqueInstanceDTO toDTO(RisqueInstance entity) {
        if (entity == null) return null;

        RisqueInstanceDTO dto = new RisqueInstanceDTO();
        dto.setId(entity.getId());
        dto.setName(entity.getName());
        dto.setContexte(entity.getContexte());
        dto.setNiveauRisqueResiduel(entity.getNiveauRisqueResiduel());
        dto.setNiveauIntrinseque(entity.getNiveauIntrinseque());
        dto.setTypeValidation(entity.getTypeValidation());
        dto.setValider(entity.isValider());
        dto.setInitier(entity.isInitier());
        dto.setDateValidation(entity.getDateValidation());
        dto.setComment(entity.getComment());
        dto.setAutreRisque(entity.getAutreRisque());
        dto.setCreatedAt(entity.getCreatedAt());
        dto.setUpdatedAt(entity.getUpdatedAt());

        // Map Risque
        dto.setRisque(toRisqueDTO(entity.getRisque()));

        // Map ZoneRisqueInstances
        List<ZoneRisqueInstanceDTO> zoneDTOs = entity.getZoneRisques().stream().map(zri -> {
            ZoneRisqueInstanceDTO zriDTO = new ZoneRisqueInstanceDTO();
            zriDTO.setId(zri.getId());

            // ZoneRisque
            zriDTO.setZoneRisque(toZoneRisqueDTO(zri.getZoneRisque()));

            // AnalyseInstances
            List<AnalyseInstanceDTO> analyseDTOs = zri.getAnalyseInstances().stream().map(ai -> {
                AnalyseInstanceDTO aiDTO = new AnalyseInstanceDTO();
                aiDTO.setId(ai.getId());
                aiDTO.setReponse(ai.getReponse());
                aiDTO.setFacteur(ai.getFacteur());
                aiDTO.setAnalyseRisque(toAnalyseRisqueDTO(ai.getAnalyseRisque()));
                return aiDTO;
            }).collect(Collectors.toList());

            zriDTO.setAnalyseInstances(analyseDTOs);
            return zriDTO;
        }).collect(Collectors.toList());
        dto.setZoneRisques(zoneDTOs);

        // Map Conditions
        dto.setConditions(entity.getConditions().stream().map(c -> {
            ConditionsDTO cDTO = new ConditionsDTO();
            cDTO.setId(c.getId());
            cDTO.setDetail(c.getDetail());
            cDTO.setCategorie(c.getCategorie());
            cDTO.setAssigne(c.getAssigne().getEmail());
            cDTO.setNumberSemaineApresLancement(c.getNumberSemaineApresLancement());
            cDTO.setDateLancement(c.getDateLancement());
            cDTO.setDateEcheance(c.getDateEcheance());
            cDTO.setDateColoture(c.getDateColoture());
            cDTO.setEtat(c.getEtat());
            return cDTO;
        }).collect(Collectors.toList()));

        return dto;
    }

    private static RisqueDTO toRisqueDTO(Risque r) {
        if (r == null) return null;
        RisqueDTO dto = new RisqueDTO();
        dto.setId(r.getId());
        dto.setName(r.getName());
        dto.setValidateurEmail(r.getValidateur() != null ? r.getValidateur().getEmail() : null);
        dto.setCreatedAt(r.getCreatedAt());
        dto.setUpdatedAt(r.getUpdatedAt());
        return dto;
    }

    private static ZoneRisqueDTO toZoneRisqueDTO(ZoneRisque zr) {
        if (zr == null) return null;
        ZoneRisqueDTO dto = new ZoneRisqueDTO();
        dto.setId(zr.getId());
        dto.setName(zr.getName());
        return dto;
    }

    private static AnalyseRisqueDTO toAnalyseRisqueDTO(AnalyseRisque ar) {
        if (ar == null) return null;
        AnalyseRisqueDTO dto = new AnalyseRisqueDTO();
        dto.setId(ar.getId());
        dto.setDescription(ar.getDescription());
        return dto;
    }
}
public RisqueInstanceDTO getRisqueInstanceById(Long id) {
    RisqueInstance entity = risqueInstanceRepository.findById(id)
            .orElseThrow(() -> new EntityNotFoundException("RisqueInstance not found with id = " + id));
    return RisqueInstanceMapper.toDTO(entity);
}
