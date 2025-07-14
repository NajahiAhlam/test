exportToCSV(): void {
  const visibleColumns = this.columns.filter(col => this.displayColumns.includes(col.field));

  const headers = visibleColumns.map(col => col.label);
  const rows = this.dataSource.filteredData.map(row => {
    return visibleColumns.map(col => {
      let value = row[col.field];
      if (col.field === 'statusProduit') {
        value = this.getActionLabel(value);
      }
      return value !== null && value !== undefined ? `"${value}"` : '';
    }).join(',');
  });

  const csvContent = [headers.join(','), ...rows].join('\n');

  const blob = new Blob([csvContent], { type: 'text/csv;charset=utf-8;' });
  const link = document.createElement('a');
  const url = URL.createObjectURL(blob);
  link.setAttribute('href', url);
  link.setAttribute('download', 'suivit-condition.csv');
  document.body.appendChild(link);
  link.click();
  document.body.removeChild(link);
}
<button mat-button color="primary" (click)="exportToCSV()">
  <mat-icon>file_download</mat-icon> Export CSV
</button>
