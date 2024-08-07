<nb-card>
  <nb-card-header>
    <div>
      Répartition par Type Agile
      <nb-icon icon="nb-bar-chart" (click)="togglePieChart()" class="ml-2"></nb-icon>
    </div>
  </nb-card-header>
  <nb-card-body>
    <table *ngIf="!showPieChart" class="table w-100">
      <thead class="bg-header fw-bold">
        <tr>
          <th>Agile</th>
          <th>Nombre de projets</th>
          <th>%CTB/RTB</th>
        </tr>
      </thead>
      <tbody>
        <tr *ngFor="let summary of data">
          <td>{{ summary.typeAgileNom }}</td>
          <td>{{ summary.countProjet }}</td>
          <td>{{ summary.percentage | number:'1.2-2' }}%</td>
        </tr>
      </tbody>
    </table>
    <div *ngIf="showPieChart" id="pie-chart-container" style="width: 100%; height: 400px;"></div>
  </nb-card-body>
</nb-card>
------------------
import { Component, OnInit } from '@angular/core';
import * as Highcharts from 'highcharts';

@Component({
  selector: 'app-dashboard',
  templateUrl: './dashboard.component.html',
  styleUrls: ['./dashboard.component.scss']
})
export class DashboardComponent implements OnInit {
  showPieChart = false;
  pieChartOptions: Highcharts.Options;

  aggregatedDataa: any; // Adjust this type based on your data model

  constructor(private analyticsService: AnalyticsService) {}

  ngOnInit(): void {
    this.analyticsService.getAggregatedDataa(this.selectedYear, this.selectedProgramme, this.selectedLead).subscribe(data => {
      this.aggregatedDataa = data;
      this.createPieChart(this.aggregatedDataa?.data); // Assuming 'data' is the field containing the pie chart data
    });
  }

  togglePieChart(): void {
    this.showPieChart = !this.showPieChart;
    if (this.showPieChart) {
      this.createPieChart(this.aggregatedDataa?.data); // Update the pie chart with data when shown
    }
  }

  createPieChart(data: any[]): void {
    const seriesData = data.map(item => ({
      name: item[0],
      y: item[1]
    }));

    this.pieChartOptions = {
      chart: {
        type: 'pie'
      },
      title: {
        text: 'Enjeux Initiative P1'
      },
      series: [{
        type: 'pie',
        name: 'Initiatives Count',
        data: seriesData,
        colors: ['#E4003A', '#4535C1', '#3357FF', '#FF33A1', '#FFBF00']
      }],
      plotOptions: {
        pie: {
          allowPointSelect: true,
          cursor: 'pointer',
          dataLabels: {
            enabled: true,
            format: '{point.name}: {point.percentage:.1f}%',
            style: {
              color: 'black'
            }
          }
        }
      },
      credits: { enabled: false }
    };

    Highcharts.chart('pie-chart-container', this.pieChartOptions);
  }
}
.ml-2 {
  margin-left: 8px;
}

.table th, .table td {
  text-align: center;
}

#pie-chart-container {
  text-align: center;
}
