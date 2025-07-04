@Entity
@Table(name="risque")
@AllArgsConstructor
@NoArgsConstructor
@Getter
@Setter
@Builder
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
@Entity
@Table(name="risque_instance")
@AllArgsConstructor
@NoArgsConstructor
@Getter
@Setter
@Builder
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
    @JoinColumn(name = "risque_id")  // This creates a column named 'risque_id'
    private Risque risque;

    @ManyToOne
    @JoinColumn(name = "demande_qualification_id")  // This creates a column named 'demande_qualification_id'
    private DemandeQualification demandeQualification;
    @OneToMany(mappedBy = "risque", cascade = CascadeType.ALL)
    private List<RisqueComment> comments =new ArrayList<>();
}
hey chat as you see i have entite risque is like a template that i difine on it the name the validateur and zoneRisque that inside it have name and analyse type Analyse and in risqueInstance 
i have the risque and zoneRisqueInstance and analyseInstance inside it it like user admin enter template of risque and the validateur enter and saisi the contexte of risqueInstace and do his analyse of the zoneRisque that the admin saisi now i have an evol is in some risque have A LIST OF QUESTION WIth boolean response the question is also parametrable in risque and the validateur answer on risqueInstance
