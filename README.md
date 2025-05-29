import { NbCalendarRange } from '@nebular/theme';

fetchByRange(range: NbCalendarRange<Date>): void {
  if (range.start && range.end) {
    this.fetchReportByDateRange(range.start, range.end);
  }
}
