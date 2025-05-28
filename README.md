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
