import { Component, OnInit } from '@angular/core';
import { HttpClient } from '@angular/common/http';

@Component({
  selector: 'app-grouped-table',
  templateUrl: './grouped-table.component.html',
  styleUrls: ['./grouped-table.component.scss']
})
export class GroupedTableComponent implements OnInit {
  rawGroupedData: any = {};
  tableData: any[] = [];
  filteredData: any[] = [];
  columns: string[] = ['assigneeName', 'title', 'status', 'date'];
  filterText: string = '';

  constructor(private http: HttpClient) {}

  ngOnInit(): void {
    this.http.get('https://your-api-url.com/your-endpoint').subscribe((data: any) => {
      this.rawGroupedData = data;
      this.tableData = this.flattenGroupedData(data);
      this.filteredData = [...this.tableData]; // initialize filtered
    });
  }

  flattenGroupedData(data: any): any[] {
    return Object.entries(data).flatMap(([assigneeName, items]: [string, any[]]) =>
      items.map(item => ({ ...item, assigneeName }))
    );
  }

  onFilterChange(): void {
    const term = this.filterText.toLowerCase();
    this.filteredData = this.tableData.filter(row =>
      this.columns.some(col =>
        (row[col] || '').toString().toLowerCase().includes(term)
      )
    );
  }
}
<nb-card>
  <nb-card-header>
    Grouped Table (Flat View with Filter)
  </nb-card-header>

  <nb-card-body>

    <input
      nbInput
      fullWidth
      placeholder="Filter..."
      [(ngModel)]="filterText"
      (ngModelChange)="onFilterChange()"
    />

    <nb-table [nbSort]="true">
      <thead>
        <tr>
          <th *ngFor="let col of columns">{{ col | titlecase }}</th>
        </tr>
      </thead>
      <tbody>
        <tr *ngFor="let row of filteredData">
          <td *ngFor="let col of columns">
            {{ row[col] }}
          </td>
        </tr>
      </tbody>
    </nb-table>
    
  </nb-card-body>
</nb-card>


