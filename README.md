 @Override
    @Transactional
    public void updateDemande(@RequestBody DemandeDTO demandeDTO) throws PermissionException, ActionNONAuthoriseException {


        if (!userService.canDo("Modifier Demande COPRO")) {
            throw new PermissionException("ressource introuvable");
        }

        System.out.println(demandeDTO);
        Optional<Demande> optionalDemande = demandeRepository.findById(demandeDTO.getId());

        if (!optionalDemande.isPresent()) {
            throw new RuntimeException("Demande a modifié est introuvable");
        }

        if (demandeDTO.getSession() == null || demandeDTO.getSession().getId() == 0) {
            demandeDTO.setSession(null); // Set session to null if ID is 0 or session is null
        }
        Demande demande = modelMapper.map(demandeDTO, Demande.class);
        List<String> statusList = Arrays.asList("COPRO", "rejetée", "validé");

        if (statusList.contains(demande.getStatusFicheCopro())) {
            throw new ActionNONAuthoriseException("Action non authoriser");
        }
        demande.getDispositifRh().forEach(dispositifRh -> {
            dispositifRh.setDemande(demande);

            dispositifRhRepository.save(dispositifRh);

        });
       demande.getChargeCout().forEach(chargeCout -> {
            chargeCout.setDemande(demande);

            chargeCoutRepository.save(chargeCout);

        });

        Demande saveDemande = demandeRepository.save(demande);
        DemandeDTO map = modelMapper.map(saveDemande, DemandeDTO.class);
       // return map;
    }
public class Demande {
    @OneToMany(mappedBy = "demande")
    private List<ChargeCout> chargeCout;
}
public class ChargeCout {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

   @ManyToOne
   private TypePrestation typePrestation;


    @ManyToOne
    @JoinColumn(name = "fournisseur_id", nullable = true)
    private Fournisseur fournisseur;

    @Column(columnDefinition="text")
    private String description;
    private String charge;
    private String coutInvestissement;
    private String coutDeFonctionnement;
    @Column(columnDefinition="text")
    private String remarque;

    @ManyToOne
    private Lead entiteEnCharge;

    @ManyToOne
    @JoinColumn(name="demande_id", nullable=false)
    @JsonIgnore
    private Demande demande;

}
hey i have in update demande my front send an objet demande inside list of chargeCourt in chargeCour have relation fournisseur that in my objet not always obligatoir to be in all objet if list chargeCout that in demande that get somtimes
nullPointer
