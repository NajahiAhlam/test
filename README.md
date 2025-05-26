public class FullReportingDTO {
    private Long demandeId;
    private String nomFiliale;
    private String objetDemande;
    private Long numeroDemande;
    private LocalDateTime dateQualification;

    private Long risqueId;
    private String risqueName;
    private String niveauRisqueResiduel;
    private String niveauIntrinseque;

    private Long conditionId;
    private String detail;
    private String categorie;
    private LocalDate dateLancement;
    private Long numberSemaineApresLancement;
    private LocalDate dateEcheanceEffective;

    // Constructor, Getters, Setters...
}
@Query("SELECT DISTINCT d FROM DemandeQualification d " +
       "LEFT JOIN FETCH d.risques r " +
       "LEFT JOIN FETCH r.conditions c")
List<DemandeQualification> findAllWithRisquesAndConditions();
public List<FullReportingDTO> getFullReport(LocalDate startDate, LocalDate endDate) {
    List<DemandeQualification> demandes = demandeQualificationRepository.findAllWithRisquesAndConditions();

    List<FullReportingDTO> report = new ArrayList<>();

    for (DemandeQualification d : demandes) {
        if (d.getRisques() == null) continue;

        for (RisqueInstance r : d.getRisques()) {
            if (r.getConditions() == null) continue;

            for (Conditions c : r.getConditions()) {
                LocalDate effectiveDateEcheance;

                if ("postCondition".equalsIgnoreCase(c.getCategorie()) &&
                    c.getDateLancement() != null &&
                    c.getNumberSemaineApresLancement() != null) {
                    effectiveDateEcheance = c.getDateLancement()
                        .plusWeeks(c.getNumberSemaineApresLancement());
                } else {
                    effectiveDateEcheance = c.getDateEcheance();
                }

                if (effectiveDateEcheance != null &&
                    !effectiveDateEcheance.isBefore(startDate) &&
                    !effectiveDateEcheance.isAfter(endDate)) {

                    FullReportingDTO dto = new FullReportingDTO();
                    dto.setDemandeId(d.getId());
                    dto.setNomFiliale(d.getNomFiliale());
                    dto.setObjetDemande(d.getObjetDemande());
                    dto.setNumeroDemande(d.getNumeroDemande());
                    dto.setDateQualification(d.getDateQualification());

                    dto.setRisqueId(r.getId());
                    dto.setRisqueName(r.getName());
                    dto.setNiveauRisqueResiduel(r.getNiveauRisqueResiduel());
                    dto.setNiveauIntrinseque(r.getNiveauIntrinseque());

                    dto.setConditionId(c.getId());
                    dto.setDetail(c.getDetail());
                    dto.setCategorie(c.getCategorie());
                    dto.setDateLancement(c.getDateLancement());
                    dto.setNumberSemaineApresLancement(c.getNumberSemaineApresLancement());
                    dto.setDateEcheanceEffective(effectiveDateEcheance);

                    report.add(dto);
                }
            }
        }
    }

    return report;
}
@RestController
@RequestMapping("/api/reporting")
@RequiredArgsConstructor
public class ReportingController {

    private final ReportingService reportingService;

    @GetMapping
    public ResponseEntity<List<FullReportingDTO>> getReport(
            @RequestParam("start") @DateTimeFormat(iso = DateTimeFormat.ISO.DATE) LocalDate start,
            @RequestParam("end") @DateTimeFormat(iso = DateTimeFormat.ISO.DATE) LocalDate end) {
        List<FullReportingDTO> report = reportingService.getFullReport(start, end);
        return ResponseEntity.ok(report);
    }
}
// full-reporting.model.ts
export interface FullReporting {
  demandeId: number;
  nomFiliale: string;
  objetDemande: string;
  numeroDemande: number;
  dateQualification: string;

  risqueId: number;
  risqueName: string;
  niveauRisqueResiduel: string;
  niveauIntrinseque: string;

  conditionId: number;
  detail: string;
  categorie: string;
  dateLancement: string;
  numberSemaineApresLancement: number;
  dateEcheanceEffective: string;
}
// reporting.service.ts
import { HttpClient, HttpParams } from '@angular/common/http';
import { Injectable } from '@angular/core';
import { FullReporting } from './full-reporting.model';
import { Observable } from 'rxjs';

@Injectable({ providedIn: 'root' })
export class ReportingService {
  private readonly apiUrl = '/api/reporting';

  constructor(private http: HttpClient) {}

  getReport(start: string, end: string): Observable<FullReporting[]> {
    const params = new HttpParams().set('start', start).set('end', end);
    return this.http.get<FullReporting[]>(this.apiUrl, { params });
  }
}
// reporting.component.ts
import { Component } from '@angular/core';
import { ReportingService } from './reporting.service';
import { FullReporting } from './full-reporting.model';

@Component({
  selector: 'app-reporting',
  templateUrl: './reporting.component.html',
})
export class ReportingComponent {
  reports: FullReporting[] = [];
  startDate: string = '';
  endDate: string = '';
  loading = false;

  constructor(private reportingService: ReportingService) {}

  fetchReport() {
    if (!this.startDate || !this.endDate) return;
    this.loading = true;
    this.reportingService.getReport(this.startDate, this.endDate).subscribe({
      next: data => {
        this.reports = data;
        this.loading = false;
      },
      error: err => {
        console.error(err);
        this.loading = false;
      }
    });
  }
}

