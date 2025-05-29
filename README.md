<div class="rangeCalendar ml-3"
     title="vs {{ datePreFrom | date:'dd/MM/yyyy' }} - {{ datePreTo | date:'dd/MM/yyyy' }}">
  <input class="form-control"
         placeholder="{{ datePreFrom | date:'shortDate' }} - {{ datePreTo | date:'shortDate' }}"
         [nbDatepicker]="rangepicker"
         size="21" />

  <nb-rangepicker #rangepicker
                  [range]="{ start: datePreFrom, end: datePreTo }"
                  [max]="maxDateRange"
                  format="dd/MM/yyyy"
                  (rangeChange)="fetchByRange($event)">
  </nb-rangepicker>
</div>
fetchByRange(range: { start: Date; end: Date }): void {
  if (range.start && range.end) {
    this.fetchReportByDateRange(range.start, range.end);
  }
}
