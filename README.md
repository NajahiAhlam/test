@Entity
@Table(name="zone_risque_instance")
@AllArgsConstructor
@NoArgsConstructor
@Getter
@Setter
@Builder
public class ZoneRisqueInstance {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    @ManyToOne
    private ZoneRisque zoneRisque;
    @OneToMany
    private List<AnalyseInstance> analyseInstances;
}
@Entity
@Table(name="analyse_instance")
@AllArgsConstructor
@NoArgsConstructor
@Getter
@Setter
@Builder
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
    private Risque risque;
}
give me the map entite correct 
in dto came like this
form value:  
{risqueId: 1, contexte: 'zez', niveauIntrinseque: 'Faible  ', autreRisque: 'zez', niveauRisqueResiduel: 'Faible  ', …}
autreRisque
: 
"zez"
comment
: 
""
conditions
: 
Array(1)
0
: 
assigne
: 
"julien.blanc@example.com"
categorie
: 
"précondition"
commentCond
: 
"zeze"
dateEcheance
: 
Tue May 20 2025 00:00:00 GMT+0100 (UTC+01:00) {}
dateLancement
: 
""
detail
: 
"zdez"
etat
: 
""
id
: 
null
numberSemaineApresLancement
: 
""
[[Prototype]]
: 
Object
length
: 
1
[[Prototype]]
: 
Array(0)
contexte
: 
"zez"
niveauIntrinseque
: 
"Faible  "
niveauRisqueResiduel
: 
"Faible  "
risqueId
: 
1
zoneRisqueInstance
: 
Array(2)
0
: 
AnalyseRisqueInstance
: 
Array(1)
0
: 
analyseRisqueId
: 
1
facteur
: 
""
reponse
: 
""
[[Prototype]]
: 
Object
length
: 
1
[[Prototype]]
: 
Array(0)
name
: 
"test"
zoneRisqueId
: 
1
[[Prototype]]
: 
Object
1
: 
AnalyseRisqueInstance
: 
[{…}]
name
: 
"test 2"
zoneRisqueId
: 
2
[[Prototype]]
: 
Object
length
: 
2
[[Prototype]]
: 
Array(0)
[[Prototype]]
: 
Object
