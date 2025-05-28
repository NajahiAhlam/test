groupedRows: {
  assigneeName: string;
  rows: any[];
  rowspan: number;
  sumNbrS: number;
}[] = [];
buildGroupedRows() {
  const map = new Map<string, any[]>();

  for (const row of this.filteredData) {
    const key = row.assigneeName ?? 'Unknown';
    if (!map.has(key)) {
      map.set(key, []);
    }
    map.get(key)!.push(row);
  }

  this.groupedRows = [];
  map.forEach((rows, assigneeName) => {
    const sumNbrS = rows.reduce((acc, r) => acc + (Number(r.NbrS) || 0), 0);
    this.groupedRows.push({
      assigneeName,
      rows,
      rowspan: rows.length,
      sumNbrS
    });
  });
}
onColumnFilterChange(): void {
  this.filteredData = this.tableData.filter(row =>
    this.columns.every(col => {
      const filterValue = this.columnFilters[col.key].toLowerCase();
      const rowValue = (row[col.key] ?? '').toString().toLowerCase();
      return rowValue.includes(filterValue);
    })
  );
  this.buildGroupedRows();
}
this.analyticService.getReportConditionByAssigne().subscribe(
  (data) => {
    const typedData = data as Record<string, any[]>;
    this.rawGroupedData = typedData;
    this.tableData = this.flattenGroupedData(typedData);
    this.filteredData = [...this.tableData];
    this.initializeFilters();
    this.buildGroupedRows();
  },
  (error) => {
    console.error('Error fetching reports:', error);
  }
);
<table class="table w-100">
  <thead class="bg-header fw-bold">
    <tr>
      <th *ngFor="let col of columns">{{ col.label | titlecase }}</th>
    </tr>
    <tr>
      <th *ngFor="let col of columns">
        <input
          nbInput
          fullWidth
          class="form-control"
          [(ngModel)]="columnFilters[col.key]"
          (ngModelChange)="onColumnFilterChange()"
          placeholder="{{ col.label }}"
        />
      </th>
    </tr>
  </thead>
  <tbody>
    <ng-container *ngFor="let group of groupedRows">
      <tr *ngFor="let row of group.rows; let i = index">
        <td *ngFor="let col of columns; let c = index">
          
          <!-- For assigneeName, show once with rowspan -->
          <ng-container *ngIf="col.key === 'assigneeName'">
            <span *ngIf="i === 0" [attr.rowspan]="group.rowspan">
              {{ group.assigneeName | mail }}
            </span>
          </ng-container>
          
          <!-- For NbrS, sum for the group and show in first row -->
          <ng-container *ngIf="col.key === 'NbrS'">
            <span *ngIf="i === 0" [attr.rowspan]="group.rowspan">
              {{ group.sumNbrS }}
            </span>
          </ng-container>
          
          <!-- For other columns -->
          <ng-container *ngIf="col.key !== 'assigneeName' && col.key !== 'NbrS'">
            <ng-container [ngSwitch]="col.key">

              <ng-container *ngSwitchCase="'validateur'">
                {{ row[col.key] | mail }}
              </ng-container>

              <ng-container *ngSwitchCase="'categorie'">
                {{ getCategorieClass(row[col.key]) }}
              </ng-container>

              <ng-container *ngSwitchCase="'productStatus'">
                {{ getActionLabel(row[col.key]) }}
              </ng-container>

              <ng-container *ngSwitchDefault>
                {{ row[col.key] }}
              </ng-container>

            </ng-container>
          </ng-container>
          
        </td>
      </tr>
    </ng-container>
  </tbody>
</table>
