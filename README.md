this.http.get<Record<string, any[]>>('https://your-api-url.com/your-endpoint')
  .subscribe(data => {
    this.rawGroupedData = data;
    this.tableData = this.flattenGroupedData(data);
    this.filteredData = [...this.tableData];
  });
flattenGroupedData(data: Record<string, any[]>): any[] {
  return Object.entries(data).flatMap(([assigneeName, items]) =>
    items.map(item => ({ ...item, assigneeName }))
  );
}
