public class AnalyseInstance {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    @Column(columnDefinition="text")
    private String reponse;
    private String facteur;
    @ManyToOne
    private AnalyseRisque analyseRisque;
}
public class AnalyseRisque {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    @Column(columnDefinition="text")
    private String description;
}
public class Conditions {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    @Column(columnDefinition="text")
    private String detail;
    private String categorie;
    @ManyToOne
    private User assigne;
    @OneToMany(mappedBy = "conditions", cascade = CascadeType.ALL)
    private List<ConditionComment> comments =new ArrayList<>();
    private Long numberSemaineApresLancement;
    private LocalDate dateLancement;
    private LocalDate dateEcheance;
    private LocalDateTime dateColoture;
    private String etat;
    @OneToMany(mappedBy="condition",fetch = FetchType.LAZY)
    private List<AttachementClotureCondition> attachments=new ArrayList<>();
}
public class Risque {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    @ManyToOne
    private User validateur;
    @OneToMany
    private List<ZoneRisque> zoneRisques;
    @Column(updatable = false)
    @CreationTimestamp
    private LocalDateTime createdAt;
    @UpdateTimestamp
    private LocalDateTime updatedAt;
}
public class RisqueInstance {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    @Column(columnDefinition = "TEXT")
    private String contexte;
    private String niveauRisqueResiduel;
    private String NiveauIntrinseque;
    @OneToMany
    private List<ZoneRisqueInstance> zoneRisques;
    @OneToMany
    private List<Conditions> conditions;
    private String typeValidation;
    @Column(updatable = false)
    @CreationTimestamp
    private LocalDateTime createdAt;
    @UpdateTimestamp
    private LocalDateTime updatedAt;
    private boolean valider=false;
    private boolean initier=false;
    private LocalDateTime dateValidation;
    @Column(columnDefinition = "TEXT")
    private String comment;
    @Column(columnDefinition = "TEXT")
    private String autreRisque;
    @ManyToOne
    private Risque risque;
}
public class ZoneRisque {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    @OneToMany
    private List<AnalyseRisque> analyseRisques;
}
public class ZoneRisqueInstance {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    @ManyToOne
    private ZoneRisque zoneRisque;
    @OneToMany
    private List<AnalyseInstance> analyseInstances;
}
hey chat i have those entities i ahev user with role admin enter risque and zonerisque and analyse like template and the validateur that admin enter on risque and enter the risqueInstance and zoneRisque analyseInstance repondre the create i did of the template and of the risqueInstance this is the create methode of risqueInstance 
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
        Risque risque = risqueRepository.findById(dto.getRisqueId())
                .orElseThrow(() -> new IllegalArgumentException("Aucune risque avec id = " + dto.getRisqueId()));
        risqueInstance.setRisque(risque);
        risqueInstance.setZoneRisques(zoneRisqueInstances);
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
hey i want you to create for me dtos of theme and mapper and getById want it to come like this risqueInstance all his variable and risque with his variable and zoneRisqueInstance with zoneRisque and  analyseInstance  
