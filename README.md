<div class="rangeCalendar ml-3"
     title="vs {{ datePreFrom | date:'dd/MM/yyyy' }} - {{ datePreTo | date:'dd/MM/yyyy' }}">
  <input class="form-control"
         placeholder="{{ datePreFrom | date:'shortDate' }} - {{ datePreTo | date:'shortDate' }}"
         [nbDatepicker]="rangepicker"
         size="21" />

  <nb-rangepicker #rangepicker
                  [start]="datePreFrom"
                  [end]="datePreTo"
                  [max]="maxDateRange"
                  format="dd/MM/yyyy"
                  (rangeChange)="fetchByRange($event)">
  </nb-rangepicker>
</div>
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-your-component',
  templateUrl: './your-component.component.html',
})
export class YourComponent implements OnInit {
  datePreFrom: Date;
  datePreTo: Date;
  maxDateRange = new Date();

  constructor(private analyticService: AnalyticService) {}

  ngOnInit(): void {
    const now = new Date();
    const firstDayPrevMonth = new Date(now.getFullYear(), now.getMonth() - 1, 1);
    const lastDayPrevMonth = new Date(now.getFullYear(), now.getMonth(), 0); // 0th of this month = last of previous

    this.datePreFrom = firstDayPrevMonth;
    this.datePreTo = lastDayPrevMonth;

    // Initial fetch with previous month's range
    this.fetchReportByDateRange(this.datePreFrom, this.datePreTo);
  }

  fetchReportByDateRange(start: Date, end: Date): void {
    this.datePreFrom = start;
    this.datePreTo = end;

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

  fetchByRange(range: { start: Date; end: Date }): void {
    if (range.start && range.end) {
      this.fetchReportByDateRange(range.start, range.end);
    }
  }
}
