  <div class="rangeCalendar ml-3"
           title="vs {{datePreFrom|date:'dd/MM/yyyy'}} - {{datePreTo|date:'dd/MM/yyyy'}}">
        <input class="form-control" placeholder="{{dateFrom|date:'shortDate'}} - {{dateTo|date:'shortDate'}} "
               [nbDatepicker]="rangepicker" size="21" />

        <nb-rangepicker (rangeChange)=fetchByRange($event) format="dd/MM/yyyy" date #rangepicker
                        [max]="maxDateRange"></nb-rangepicker>
      </div>
      fetchReportByDateRange(start: Date, end: Date): void {
   this.analyticService.getReportConditionByAssigne(start, end).subscribe(
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
}
hey i want the input of date to take by default vaulue range of mounth precedent and when user change the range the api take the start and end that he do always take the range of inout
