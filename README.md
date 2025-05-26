hey chat this is my entities and relations
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
    @Lob
    private String attachBase64; // Contenu du fichier encodé en Base64

    @NotNull(message = "Porteur projet est obligatoire")
    @ManyToOne
    private User porteurProjet;

    @NotNull(message = "Porteur métier est obligatoire")
    @ManyToOne
    private User porteurMetier;

    @Enumerated(EnumType.STRING)
    private Statut statut;

    @NotNull(message = "Leader est obligatoire")
    @ManyToOne
    private User reporter;

    @Enumerated(EnumType.STRING)
    private Decision decision;
    @Enumerated(EnumType.STRING)
    private Criticite criticite;
    @Enumerated(EnumType.STRING)
    private Local local;
    private boolean transmet;
    private boolean transmetSponsor;
    private Date dateKickOff;
     private String commentaire;
    @ManyToOne
    private User coordinateurCNP;
    @ManyToOne
    private User sponsorM;

    private Long numeroDemande;
    private LocalDateTime dateQualification ;
    private Long risquesAjoutes;
    @OneToMany
    private List<RisqueInstance> risques;

    private String etape;
   /* @OneToMany(mappedBy = "demandeQualification", cascade = CascadeType.ALL, fetch = FetchType.EAGER)
    @JsonManagedReference
    private List<DemandeQualificationQuestionReponse> demandeQualificationQuestionReponses;*/
    @OneToMany(mappedBy = "demandeQualification", cascade = CascadeType.ALL, fetch = FetchType.EAGER)
    private List<DemandeQualificationQuestionReponse> demandeQualificationQuestionReponses= new ArrayList<>();;
    @Column(updatable = false)
    @CreationTimestamp
    private LocalDateTime dateCreation;
    @UpdateTimestamp
    private LocalDateTime dateModification;

    @OneToMany(mappedBy = "demandeQualification", cascade = CascadeType.ALL)
    private List<Comment> comments =new ArrayList<>();
    @OneToMany(mappedBy="demandeQualification",fetch = FetchType.LAZY)
    private List<DemandeHistory> demandeHistories=new ArrayList<>();
    @OneToMany(mappedBy="demandeQualification",fetch = FetchType.LAZY)
    private List<Attachment> attachments=new ArrayList<>();


    @ManyToOne
    @JoinColumn(name = "assigne_id")
    private User assigne;

    // List of people
    @OneToMany(mappedBy = "demandeQualification", fetch = FetchType.LAZY)
    private List<People> peopeles;

    private String status;
    private String kickOffDecision;

    @CreationTimestamp
    private LocalDateTime createdAt;

    @UpdateTimestamp
    private LocalDateTime updatedAt;

    private String processInstanceId;
    private String currentActivityId;
    private String currentActivityName;
    private String codeProduit;
    private String nomProduit;
    @Column(columnDefinition="text")
    private String descriptionProduit;
    private Date datePreCnp;
    private Date dateCnp;
    private String typeCnp;
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
i want to do a reporting getting the demande and risque and conditions filter by  plage of date is dateEchoue in conditions if conditions.categorie is postCondition the dateEchoue calcul numberSemaineApresLancement+dateLancement and if conditions.categorie is preCondition i have dateEchoue comme attribut fill
