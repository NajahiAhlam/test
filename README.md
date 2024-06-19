.small-cards {
  width: 100%;
  height: 100%;
}

.icon-row {
  background-color: #f7f7f7;
  border-radius: 50%;
  padding: 10px;
}

.title-row p {
  margin: 0;
}

.stats-row h4 {
  margin: 0;
}


export class ChartExampleComponent implements OnInit {
  Highcharts: typeof Highcharts = Highcharts;
  chartOptions: Highcharts.Options;
  pieChartOptions: Highcharts.Options;
  barChartOptions: Highcharts.Options;
  selectedIntervenant: string = 'All';
  selectedStatus: string = 'All';

  intervenants = ['All', 'Intervenant 1', 'Intervenant 2'];
  statuses = ['All', 'Status 1', 'Status 2'];

  cards = [
    { icon: 'list', title: 'Nombre Total Factures', value: 133 },
    { icon: 'truck', title: 'Nombre Factures Pénalités', value: 0 },
    { icon: 'check', title: 'Volumne Factures (MAD)', value: 3 },
    { icon: 'money-bill', title: 'Budget', value: 177690.6 }
  ];

  ngOnInit(): void {
    this.initializeCharts();
  }

  initializeCharts() {
    this.chartOptions = {
      chart: {
        type: 'column'
      },
      title: {
        text: "Born persons, by girls' name"
      },
      xAxis: {
        categories: ['2019', '2020', '2021']
      },
      yAxis: {
        title: {
          text: 'Amount'
        }
      },
      series: [{
        type: 'column',
        name: 'Ava',
        data: [38, 51, 34]
      }, {
        type: 'column',
        name: 'Dina',
        data: [31, 26, 27]
      }, {
        type: 'column',
        name: 'Malin',
        data: [38, 42, 41]
      }]
    };

    this.pieChartOptions = {
      chart: {
        type: 'pie'
      },
      title: {
        text: 'Factures Distribution'
      },
      series: [{
        type: 'pie',
        name: 'Factures',
        data: [
          { name: 'Facture 1', y: 61.41 },
          { name: 'Facture 2', y: 11.84 },
          { name: 'Facture 3', y: 10.85 },
          { name: 'Facture 4', y: 4.67 },
          { name: 'Facture 5', y: 4.18 }
        ]
      }]
    };

    this.barChartOptions = {
      chart: {
        type: 'bar'
      },
      title: {
        text: 'Factures par Status/Mois'
      },
      xAxis: {
        categories: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul']
      },
      series: [{
        type: 'bar',
        name: 'Facture Status 1',
        data: [10, 15, 20, 25, 30, 35, 40]
      }, {
        type: 'bar',
        name: 'Facture Status 2',
        data: [5, 10, 15, 20, 25, 30, 35]
      }]
    };
  }

  filterCharts() {
    // Add your filtering logic here based on selectedIntervenant and selectedStatus
    console.log('Selected Intervenant:', this.selectedIntervenant);
    console.log('Selected Status:', this.selectedStatus);
  }
}


<nb-card accent="primary">
  <ng-container>
    <nb-card-header class="d-flex flex-row justify-content-between">
      <h5 class="title-animation title-heading text-uppercase my-auto p-2">Billing Tracking</h5>
      <div>
        <nb-select [(selected)]="selectedIntervenant" (selectedChange)="filterCharts()" placeholder="Intervenant">
          <nb-option *ngFor="let intervenant of intervenants" [value]="intervenant">{{ intervenant }}</nb-option>
        </nb-select>
        <nb-select [(selected)]="selectedStatus" (selectedChange)="filterCharts()" placeholder="Status">
          <nb-option *ngFor="let status of statuses" [value]="status">{{ status }}</nb-option>
        </nb-select>
      </div>
    </nb-card-header>
    <nb-card-body>
      <div class="row d-flex flex-row justify-content-around mt-2">
        <div class="col-lg-2 my-5" *ngFor="let card of cards">
          <div class="card small-cards shadow mb-5 bg-white">
            <div class="card-body d-flex flex-column">
              <div class="icon-row d-flex justify-content-center align-items-center shadow">
                <nb-icon [icon]="card.icon" pack="fas" style="font-size: 40px;"></nb-icon>
              </div>
              <div class="title-row mt-2 pb-0">
                <p class="fs-5">{{ card.title }}</p>
              </div>
              <div class="stats-row align-self-end justify-content-start pb-0">
                <h4>{{ card.value }}</h4>
              </div>
            </div>
          </div>
        </div>
      </div>
      <nb-card class="mt-4 col-lg-6" style="padding:2%">
        <nb-card-header>
          <h5 class="fs-5">Factures par Status/Mois (Uniquement les 7 derniers mois) :</h5>
        </nb-card-header>
        <nb-card-body>
          <highcharts-chart
            [Highcharts]="Highcharts"
            [options]="barChartOptions"
            style="width: 100%; height: 400px; display: block;">
          </highcharts-chart>
        </nb-card-body>
      </nb-card>
      <nb-card class="mt-4 col-lg-6" style="padding:2%">
        <nb-card-header>
          <h5 class="fs-5">Factures Distribution :</h5>
        </nb-card-header>
        <nb-card-body>
          <highcharts-chart
            [Highcharts]="Highcharts"
            [options]="pieChartOptions"
            style="width: 100%; height: 400px; display: block;">
          </highcharts-chart>
        </nb-card-body>
      </nb-card>
    </nb-card-body>
  </ng-container>
</nb-card>








import { Component, OnInit } from '@angular/core';
import * as Highcharts from 'highcharts';

@Component({
  selector: 'app-chart-example',
  templateUrl: './chart-example.component.html',
  styleUrls: ['./chart-example.component.css']
})
export class ChartExampleComponent implements OnInit {
  Highcharts: typeof Highcharts = Highcharts;
  chartOptions: Highcharts.Options;
  pieChartOptions: Highcharts.Options;
  barChartOptions: Highcharts.Options;
  selectedIntervenant: string = 'All';
  selectedStatus: string = 'All';

  intervenants = ['All', 'Intervenant 1', 'Intervenant 2'];
  statuses = ['All', 'Status 1', 'Status 2'];

  ngOnInit(): void {
    this.initializeCharts();
  }

  initializeCharts() {
    this.chartOptions = {
      chart: {
        type: 'column'
      },
      title: {
        text: "Born persons, by girls' name"
      },
      xAxis: {
        categories: ['2019', '2020', '2021']
      },
      yAxis: {
        title: {
          text: 'Amount'
        }
      },
      series: [{
        type: 'column',
        name: 'Ava',
        data: [38, 51, 34]
      }, {
        type: 'column',
        name: 'Dina',
        data: [31, 26, 27]
      }, {
        type: 'column',
        name: 'Malin',
        data: [38, 42, 41]
      }]
    };

    this.pieChartOptions = {
      chart: {
        type: 'pie'
      },
      title: {
        text: 'Factures Distribution'
      },
      series: [{
        type: 'pie',
        name: 'Factures',
        data: [
          { name: 'Facture 1', y: 61.41 },
          { name: 'Facture 2', y: 11.84 },
          { name: 'Facture 3', y: 10.85 },
          { name: 'Facture 4', y: 4.67 },
          { name: 'Facture 5', y: 4.18 }
        ]
      }]
    };

    this.barChartOptions = {
      chart: {
        type: 'bar'
      },
      title: {
        text: 'Factures par Status/Mois'
      },
      xAxis: {
        categories: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul']
      },
      series: [{
        type: 'bar',
        name: 'Facture Status 1',
        data: [10, 15, 20, 25, 30, 35, 40]
      }, {
        type: 'bar',
        name: 'Facture Status 2',
        data: [5, 10, 15, 20, 25, 30, 35]
      }]
    };
  }

  filterCharts() {
    // Add your filtering logic here based on selectedIntervenant and selectedStatus
  }
}

