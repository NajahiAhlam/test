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
}
hey create for me methode in backend service want to add conditions pass in parametre idRisque and list of conditions and comment 
    initForm(risque?: RisqueInstance) {
      this.form = this.formBuilder.group({
        validation: [''],
        comment: [''],
          conditions: this.formBuilder.array(
          risque?.conditions?.length
            ? risque.conditions.map(c => this.createConditionGroup(c))
            : []
        ),

      });
    }
i have this if there is conditions set in risque dataValidation localDateTime.now and set isValider true and add conditions list with risque if there is comment and no condition
set in  dataValidation localDateTime.now and set isValider true and in risque comment the comment and set in typeValidation validation that came in form
