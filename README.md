import { Component, OnInit } from '@angular/core';
import { AnalyticService } from './analytic.service'; // adjust path as needed

@Component({
  selector: 'app-pilotage-conditions',
  templateUrl: './pilotage-conditions.component.html',
  styleUrls: ['./pilotage-conditions.component.scss']
})
export class PilotageConditionsComponent implements OnInit {
  rawGroupedData: Record<string, any[]> = {};
  tableData: any[] = [];
  filteredData: any[] = [];

  columns: string[] = ['assigneeName', 'title', 'status', 'date'];
  columnFilters: { [key: string]: string } = {};

  constructor(private analyticService: AnalyticService) {}

  ngOnInit(): void {
    this.analyticService.getReportConditionByAssigne().subscribe(
      (data) => {
        const typedData = data as Record<string, any[]>;
        this.rawGroupedData = typedData;
        this.tableData = this.flattenGroupedData(typedData);
        this.filteredData = [...this.tableData];
        this.initializeFilters();
      },
      (error) => {
        console.error('Error fetching reports:', error);
      }
    );
  }

  flattenGroupedData(data: Record<string, any[]>): any[] {
    return Object.entries(data).flatMap(([assigneeName, items]) =>
      items.map(item => ({ ...item, assigneeName }))
    );
  }

  initializeFilters(): void {
    this.columns.forEach(col => this.columnFilters[col] = '');
  }

  onColumnFilterChange(): void {
    this.filteredData = this.tableData.filter(row =>
      this.columns.every(col =>
        row[col]?.toString().toLowerCase().includes(this.columnFilters[col].toLowerCase())
      )
    );
  }
}
<nb-card>
  <nb-card-header>Grouped Table (With Column Filters)</nb-card-header>

  <nb-card-body>

    <table class="table table-bordered w-100">
      <thead>
        <tr>
          <th *ngFor="let col of columns">{{ col | titlecase }}</th>
        </tr>
        <tr>
          <th *ngFor="let col of columns">
            <input
              nbInput
              fullWidth
              placeholder="Filter {{ col }}"
              [(ngModel)]="columnFilters[col]"
              (ngModelChange)="onColumnFilterChange()"
            />
          </th>
        </tr>
      </thead>
      <tbody>
        <tr *ngFor="let row of filteredData">
          <td *ngFor="let col of columns">
            {{ row[col] }}
          </td>
        </tr>
      </tbody>
    </table>

  </nb-card-body>
</nb-card>
