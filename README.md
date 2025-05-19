getRisquesDisponibles(demandeId: number): Observable<Risque[]> {
    return this.http.get<Risque[]>(`${this.apiUrl}/disponibles/${demandeId}`);
  }

  import { Component, Input, OnInit } from '@angular/core';
import { RisqueService, Risque } from '../services/risque.service';

@Component({
  selector: 'app-risque-selection',
  templateUrl: './risque-selection.component.html'
})
export class RisqueSelectionComponent implements OnInit {
  @Input() demandeId: number;
  risquesDisponibles: Risque[] = [];
  loading = false;

  constructor(private risqueService: RisqueService) {}

  ngOnInit() {
    if (this.demandeId) {
      this.loadRisques();
    }
  }

  loadRisques() {
    this.loading = true;
    this.risqueService.getRisquesDisponibles(this.demandeId).subscribe({
      next: (data) => {
        this.risquesDisponibles = data;
        this.loading = false;
      },
      error: (err) => {
        console.error('Erreur chargement risques', err);
        this.loading = false;
      }
    });
  }
}
