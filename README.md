<nb-card accent="primary">
  <ng-container>
    <ng-container>
      <nb-card-body>
        <!-- Optional: Add any alerts if needed -->
      </nb-card-body>
    </ng-container>
    <ng-container>
      <nb-card-header class="d-flex flex-row justify-content-between">
        <h5 class="title-animation title-heading text-uppercase my-auto p-2">Billing Tracking</h5>
        <nb-select [(selected)]="selectedDate" placeholder="date de chargement">
          <nb-option value="2024-01-01">2024-01-01</nb-option>
        </nb-select>
      </nb-card-header>
      <nb-card-body>
        <!-- Filter for Cards -->
        <div class="row d-flex flex-row justify-content-around mt-2">
          <div class="col-lg-12 mb-4 d-flex justify-content-end">
            <nb-select [(selected)]="selectedStatus" placeholder="Filter by Status">
              <nb-option *ngFor="let status of statuses" [value]="status">{{ status }}</nb-option>
            </nb-select>
          </div>
          <!-- Cards for different metrics -->
          <div class="col-lg-2 my-5" *ngFor="let card of filteredCards">
            <div class="card small-cards shadow mb-5 bg-white">
              <div class="card-body d-flex flex-column">
                <div class="icon-row1 d-flex justify-content-center align-items-center shadow">
                  <nb-icon [icon]="card.icon" style="font-size: 40px;"></nb-icon>
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
        <!-- Filter for Pie Chart -->
        <div class="row d-flex flex-row justify-content-around mt-2">
          <div class="col-lg-6 mb-4 d-flex justify-content-end">
            <nb-select [(selected)]="selectedIntervenant" placeholder="Filter by Intervenant">
              <nb-option *ngFor="let intervenant of intervenants" [value]="intervenant">{{ intervenant }}</nb-option>
            </nb-select>
          </div>
          <!-- Pie Chart -->
          <nb-card class="col-lg-6" style="padding:2%">
            <nb-card-header class="key">
              <h5 class="fs-5">Factures Distribution :</h5>
            </nb-card-header>
            <nb-card-body>
              <highcharts-chart
                [Highcharts]="Highcharts"
                [options]="filteredPieChartOptions"
                style="width: 100%; height: 400px; display: block;">
              </highcharts-chart>
            </nb-card-body>
          </nb-card>
        </div>
        <!-- Other Charts -->
        <div id="divBarChart" class="mt-4 col-lg-4" style="padding:2%">
          <h5 class="fs-5">Factures par Status/Mois (Uniquement les 7 derniers mois) :</h5>
          <highcharts-chart
            [Highcharts]="Highcharts"
            [options]="chartOptions"
            style="width: 100%; height: 400px; display: block;">
          </highcharts-chart>
        </div>
        <nb-card class="mt-4 col-lg-6" style="padding:2%">
          <nb-card-header class="key">
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
        <nb-card>
          <nb-card-header>
            <h5 class="fs-5">Factures Distribution :</h5>
          </nb-card-header>
          <nb-card-body>
            <highcharts-chart
              [Highcharts]="Highcharts"
              [options]="barChartOptions"
              style="width: 100%; height: 400px; display: block;">
            </highcharts-chart>
          </nb-card-body>
        </nb-card>
        <nb-card>
          <nb-card-header>
            <h5 class="fs-5">Factures Distribution :</h5>
          </nb-card-header>
          <nb-card-body>
            <highcharts-chart
              [Highcharts]="Highcharts"
              [options]="chartOptions1"
              style="width: 100%; height: 400px; display: block;">
            </highcharts-chart>
          </nb-card-body>
        </nb-card>
      </nb-card-body>
    </ng-container>
  </ng-container>
</nb-card>

import { Component } from '@angular/core';
import * as Highcharts from 'highcharts';

@Component({
  selector: 'app-chart-example',
  templateUrl: './chart-example.component.html',
  styleUrls: ['./chart-example.component.scss']
})
export class ChartExampleComponent {
  Highcharts: typeof Highcharts = Highcharts;
  selectedDate: string = '2024-01-01';
  selectedStatus: string = '';
  selectedIntervenant: string = '';
  dates = ['2024-01-01', '2024-02-01', '2024-03-01'];
  statuses = ['All', 'Paid', 'Pending', 'Overdue'];
  intervenants = ['All', 'Intervenant1', 'Intervenant2', 'Intervenant3'];

  cards = [
    { title: 'Nombre Total Factures', value: 133, icon: 'list', status: 'All' },
    { title: 'Nombre Factures Pénalités', value: 0, icon: 'truck', status: 'All' },
    { title: 'Volumne Factures (MAD)', value: 3, icon: 'check', status: 'All' },
    { title: 'Budget', value: 177690.6, icon: 'money-bill', status: 'All' },
  ];

  filteredCards = this.cards;

  chartOptions: Highcharts.Options = {
    // Your chart options here
  };

  pieChartOptions: Highcharts.Options = {
    // Your pie chart options here
  };

  filteredPieChartOptions: Highcharts.Options = this.pieChartOptions;

  barChartOptions: Highcharts.Options = {
    // Your bar chart options here
  };

  chartOptions1: Highcharts.Options = {
    // Your chart options here
  };

  filterCards() {
    if (this.selectedStatus === 'All') {
      this.filteredCards = this.cards;
    } else {
      this.filteredCards = this.cards.filter(card => card.status === this.selectedStatus);
    }
  }

  filterPieChart() {
    if (this.selectedIntervenant === 'All') {
      this.filteredPieChartOptions = this.pieChartOptions;
    } else {
      // Update this.filteredPieChartOptions based on selectedIntervenant
    }
  }

  ngOnChanges() {
    this.filterCards();
    this.filterPieChart();
  }
}
