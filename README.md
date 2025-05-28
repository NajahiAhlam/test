<table class="table">
  <thead>
    <tr>
      <th *ngFor="let col of columns">{{ col | titlecase }}</th>
    </tr>
    <tr>
      <th *ngFor="let col of columns">
        <input
          nbInput
          fullWidth
          [(ngModel)]="columnFilters[col]"
          (ngModelChange)="onColumnFilterChange()"
          placeholder="Filter {{ col }}"
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
onColumnFilterChange(): void {
  this.filteredData = this.tableData.filter(row =>
    this.columns.every(col => {
      const filterValue = this.columnFilters[col].toLowerCase();
      const rowValue = (row[col] ?? '').toString().toLowerCase();
      return rowValue.includes(filterValue);
    })
  );
}
initializeFilters(): void {
  this.columnFilters = {};
  this.columns.forEach(col => this.columnFilters[col] = '');
}
