import { Component, OnInit } from '@angular/core';
import { HttpClient, HttpParams } from '@angular/common/http';

@Component({
  selector: 'app-report',
  templateUrl: './report.component.html',
})
export class ReportComponent implements OnInit {
  tableData: any[] = [];
  filteredData: any[] = [];

  constructor(private http: HttpClient) {}

  ngOnInit(): void {
    const now = new Date();

    // Calculate previous month start and end
    const start = new Date(now.getFullYear(), now.getMonth() - 1, 1);
    const end = new Date(now.getFullYear(), now.getMonth(), 0);

    this.fetchReportByDateRange(start, end);
  }

  fetchReportByDateRange(start: Date, end: Date): void {
    const params = new HttpParams()
      .set('startDate', start.toISOString().split('T')[0])
      .set('endDate', end.toISOString().split('T')[0]);

    this.http.get<any[]>('/api/report/with-date-range', { params }).subscribe({
      next: (data) => {
        this.tableData = data;
        this.filteredData = data;
      },
      error: (err) => console.error('Error fetching report:', err)
    });
  }
}
<nb-card>
  <nb-card-header>Rapport - Mois Précédent</nb-card-header>
  <nb-card-body>
    <nb-table [items]="filteredData" *ngIf="filteredData.length > 0">
      <ng-container *ngFor="let key of filteredData[0] | keyvalue">
        <ng-container *ngIf="key.key !== 'id'"> <!-- skip id column if needed -->
          <ng-template nbColumnDef="{{ key.key }}">
            <th nbHeaderCell *nbHeaderCellDef>{{ key.key }}</th>
            <td nbCell *nbCellDef="let row">{{ row[key.key] }}</td>
          </ng-template>
        </ng-container>
      </ng-container>

      <tr nbHeaderRow *nbHeaderRowDef="filteredData[0] ? Object.keys(filteredData[0]) : []"></tr>
      <tr nbRow *nbRowDef="let row; columns: filteredData[0] ? Object.keys(filteredData[0]) : []"></tr>
    </nb-table>

    <p *ngIf="filteredData.length === 0">Aucune donnée disponible pour le mois précédent.</p>
  </nb-card-body>
</nb-card>
