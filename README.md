@Query("SELECT r FROM Risque r WHERE r.id NOT IN " +
       "(SELECT ri.risque.id FROM RisqueInstance ri WHERE ri.demandeQualification.id = :demandeId)")
List<Risque> findRisqueNotLinkedToDemande(@Param("demandeId") Long demandeId);
@Service
public class RisqueService {

    @Autowired
    private RisqueRepository risqueRepository;

    public List<Risque> getRisquesNotInDemande(Long demandeId) {
        return risqueRepository.findRisqueNotLinkedToDemande(demandeId);
    }
}
@RestController
@RequestMapping("/api/risques")
public class RisqueController {

    @Autowired
    private RisqueService risqueService;

    @GetMapping("/disponibles/{demandeId}")
    public ResponseEntity<List<Risque>> getRisquesDisponibles(@PathVariable Long demandeId) {
        List<Risque> risques = risqueService.getRisquesNotInDemande(demandeId);
        return ResponseEntity.ok(risques);
    }
}
