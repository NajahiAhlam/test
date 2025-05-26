public class Risque {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    @ManyToOne
    private User validateur;

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
    @ManyToOne
    private User validateur;
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
    @ManyToOne
    private DemandeQualification demandeQualification;
    @OneToMany(mappedBy = "risque", cascade = CascadeType.ALL)
    private List<RisqueComment> comments =new ArrayList<>();
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
    @ManyToOne
    @JsonIgnore
    private RisqueInstance risqueInstance;
}
public class DemandeQualification {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;


    @NotNull(message = "Nom de la filiale est obligatoire")
    //@Enumerated(EnumType.STRING)
    private String nomFiliale;

    @NotNull(message = "Objet de la demande est obligatoire")
    @Size(max = 100, message = "Objet de la demande ne doit pas dépasser 100 caractères")
    private String objetDemande;

    @NotNull(message = "Type est obligatoire")
  //  @Enumerated(EnumType.STRING)
    private String type;

    @NotNull(message = "Description détaillée de la demande est obligatoire")
    @Size(max = 1550, message = "Description détaillée de la demande ne doit pas dépasser 1550 caractères")
    private String description;

    private LocalDate dateLancement;
    private LocalDate dateDemande;

    private Date dateKickOff;
    private LocalDateTime dateQualification ;
    @OneToMany
    private List<RisqueInstance> risques;

  
    @Column(updatable = false)
    @CreationTimestamp
    private LocalDateTime dateCreation;
    @UpdateTimestamp
    private LocalDateTime dateModification;

  
    private String status;
    private String kickOffDecision;

    @CreationTimestamp
    private LocalDateTime createdAt;
    @UpdateTimestamp
    private LocalDateTime updatedAt;
    private String codeProduit;
    private String nomProduit;
    @Column(columnDefinition="text")
    private String descriptionProduit;
    private Date datePreCnp;
    private Date dateCnp;
    private String typeCnp;

}
hey this my entities and relation i want to do a reporting is that i want to get for chaque validateur in entite risque the name of risque and the list of his conditions and risqueInstance
with demande filter not obligatoir with range of date in condition.dateEchoue if the conditions.categorie = postCondition if it preCondition see the numberSemaineApresLancement+dateLancement
be in that range not obligatoir the only parametre pass in qury is range date and they arent obligatoir and as you see the risque in my entitie doesnt get me the risqueInstance
