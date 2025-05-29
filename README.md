fetchReportByDateRange(start: Date, end: Date): void {
  this.datePreFrom = start;
  this.datePreTo = end;

  this.analyticService.getReportConditionByAssigne(start, end).subscribe(
    (data) => {
      const typedData = data as Record<string, any[]>;
      this.rawGroupedData = typedData;
      this.tableData = this.flattenGroupedData(typedData);

      // Apply column filters after fetching new data
      this.initializeFilters();
      this.filteredData = [...this.tableData];
      this.onColumnFilterChange();  // <<-- THIS WAS MISSING
    },
    (error) => {
      console.error('Error fetching reports:', error);
    }
  );
}
onColumnFilterChange(): void {
  this.filteredData = this.tableData.filter(row =>
    this.columns.every(col => {
      const filterValue = this.columnFilters[col.key]?.toLowerCase() || '';
      const rowValue = (row[col.key] ?? '').toString().toLowerCase();
      return rowValue.includes(filterValue);
    })
  );
  this.buildGroupedRows(); // <<-- Make sure this is here
}
