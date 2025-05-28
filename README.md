this.analyticService.getReportConditionByAssigne().subscribe(
  (data) => {
    const typedData = data as Record<string, any[]>;
    this.rawGroupedData = typedData;
    this.tableData = this.flattenGroupedData(typedData);
    this.filteredData = [...this.tableData];
  },
  (error) => {
    console.error('Error fetching reports:', error);
  }
);
getReportConditionByAssigne(): Observable<Record<string, any[]>> {
  return this.http.get<Record<string, any[]>>('/your-endpoint');
}
this.analyticService.getReportConditionByAssigne().subscribe(
  (data) => {
    this.rawGroupedData = data;
    this.tableData = this.flattenGroupedData(data);
    this.filteredData = [...this.tableData];
  },
  (error) => {
    console.error('Error fetching reports:', error);
  }
);

flattenGroupedData(data: Record<string, any[]>): any[] {
  return Object.entries(data).flatMap(([assigneeName, items]) =>
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
