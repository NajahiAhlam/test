import { Component, OnInit } from '@angular/core';
import { HttpClient } from '@angular/common/http';

@Component({
  selector: 'app-grouped-table',
  templateUrl: './grouped-table.component.html',
  styleUrls: ['./grouped-table.component.scss']
})
export class GroupedTableComponent implements OnInit {
  rawGroupedData: Record<string, any[]> = {};
  tableData: any[] = [];
  filteredData: any[] = [];

  filterText: string = '';
  columnFilters: Record<string, string> = {};

  columns = [
    { key: 'objetDemande', label: 'Titre' },
    { key: 'nomFiliale', label: 'Filiale' },
    { key: 'type', label: 'Type' },
    { key: 'dateCreation', label: 'Date de création' },
    { key: 'description', label: 'Description' },
    { key: 'dateLancement', label: 'Date de Lancement' },
    { key: 'porteurProjet', label: 'Porteur Projet' },
    { key: 'dateModification', label: 'Date de Modification' },
    { key: 'porteurMetier', label: 'Porteur Metier' },
    { key: 'status', label: 'Statut' },
    { key: 'dateKickOff', label: 'Date de KickOff' },
    { key: 'kickOffDecision', label: 'Decision kickOff' },
    { key: 'typeDemande', label: 'Type Demande' },
    { key: 'assigneeName', label: 'Assigné' }
  ];

  constructor(private http: HttpClient) {}

  ngOnInit(): void {
    this.fetchData();
  }

  fetchData(): void {
    this.http.get<Record<string, any[]>>('your-api-url').subscribe(
      data => {
        this.rawGroupedData = data;
        this.tableData = this.flattenGroupedData(data);
        this.filteredData = [...this.tableData];
        this.initializeFilters();
      },
      error => {
        console.error('Error fetching grouped reports:', error);
      }
    );
  }

  flattenGroupedData(data: Record<string, any[]>): any[] {
    return Object.entries(data).flatMap(([assigneeName, items]) =>
      items.map(item => ({ ...item, assigneeName }))
    );
  }

  initializeFilters(): void {
    this.columnFilters = {};
    this.columns.forEach(col => this.columnFilters[col.key] = '');
  }

  onColumnFilterChange(): void {
    this.filteredData = this.tableData.filter(row =>
      this.columns.every(col => {
        const filterValue = this.columnFilters[col.key].toLowerCase();
        const rowValue = (row[col.key] ?? '').toString().toLowerCase();
        return rowValue.includes(filterValue);
      })
    );
  }
}
<nb-card>
  <nb-card-header>Rapport Groupé par Assigné</nb-card-header>
  <nb-card-body>
    <table class="table table-bordered">
      <thead>
        <tr>
          <th *ngFor="let col of columns">
            {{ col.label }}
            <input
              nbInput
              fullWidth
              placeholder="Filtrer..."
              [(ngModel)]="columnFilters[col.key]"
              (ngModelChange)="onColumnFilterChange()"
            />
          </th>
        </tr>
      </thead>
      <tbody>
        <tr *ngFor="let row of filteredData">
          <td *ngFor="let col of columns">
            {{ row[col.key] }}
          </td>
        </tr>
      </tbody>
    </table>
  </nb-card-body>
</nb-card>
