import { AfterViewInit, Component } from '@angular/core';
import * as Highcharts from 'highcharts';

@Component({
  selector: 'app-billing-tracking',
  templateUrl: './billing-tracking.component.html',
  styleUrls: ['./billing-tracking.component.css']
})
export class BillingTrackingComponent implements AfterViewInit {
  Highcharts: typeof Highcharts = Highcharts;

  barChartOptions: Highcharts.Options = {
    chart: {
      type: 'bar'
    },
    title: {
      text: 'Historic World Population by Region',
      align: 'left'
    },
    subtitle: {
      text: 'Source: <a href="https://en.wikipedia.org/wiki/List_of_continents_and_continental_subregions_by_population" target="_blank">Wikipedia.org</a>',
      align: 'left'
    },
    xAxis: {
      categories: ['Africa', 'America', 'Asia', 'Europe'],
      title: {
        text: null
      },
      gridLineWidth: 1,
      lineWidth: 0
    },
    yAxis: {
      min: 0,
      title: {
        text: 'Population (millions)',
        align: 'high'
      },
      labels: {
        overflow: 'justify'
      },
      gridLineWidth: 0
    },
    tooltip: {
      valueSuffix: ' millions'
    },
    plotOptions: {
      bar: {
        borderRadius: 5,
        dataLabels: {
          enabled: true
        },
        groupPadding: 0.1
      }
    },
    legend: {
      layout: 'vertical',
      align: 'right',
      verticalAlign: 'top',
      x: -40,
      y: 80,
      floating: true,
      borderWidth: 1,
      backgroundColor: (Highcharts.defaultOptions.legend?.backgroundColor) || '#FFFFFF',
      shadow: true
    },
    credits: {
      enabled: false
    },
    series: [{
      type: 'bar',
      name: 'Year 1990',
      data: [631, 727, 3202, 721]
    }, {
      type: 'bar',
      name: 'Year 2000',
      data: [814, 841, 3714, 726]
    }, {
      type: 'bar',
      name: 'Year 2018',
      data: [1276, 1007, 4561, 746]
    }]
  };

  constructor() { }

  ngAfterViewInit(): void {
    // Initialization code if needed
  }
}
<nb-card accent="primary">
  <ng-container>
    <ng-container>
      <!-- <rpa-portal-loading></rpa-portal-loading> -->
    </ng-container>
    <ng-container>
      <nb-card-body>
        <!-- <div class="alert alert-danger" role="alert"></div> -->
      </nb-card-body>
    </ng-container>
    <ng-container>
      <nb-card-header class="d-flex flex-row justify-content-between">
        <h5 class="title-animation title-heading text-uppercase my-auto p-2">Billing Tracking</h5>
        <nb-select placeholder="date de chargement">
          <nb-option value="2024-01-01">2024-01-01</nb-option>
        </nb-select>
      </nb-card-header>
      <nb-card-body>
        <div class="row d-flex flex-row justify-content-around mt-2">
          <!-- Cards for different metrics -->
          <!-- Card 1 -->
          <div class="col-lg-2 my-5">
            <div class="card small-cards shadow mb-5 bg-white">
              <div class="card-body d-flex flex-column">
                <div class="icon-row1 d-flex justify-content-center align-items-center shadow">
                  <nb-icon icon="list" style="font-size: 40px;"></nb-icon>
                </div>
                <div class="title-row mt-2 pb-0">
                  <p class="fs-5">Nombre Total Factures</p>
                </div>
                <div class="stats-row align-self-end justify-content-start pb-0">
                  <h4>133</h4>
                </div>
              </div>
            </div>
          </div>
          <!-- Card 2 -->
          <div class="col-lg-2 my-5">
            <div class="card small-cards shadow mb-5 bg-white">
              <div class="card-body d-flex flex-column">
                <div class="icon-row2 d-flex justify-content-center align-items-center shadow">
                  <nb-icon icon="truck" pack="fas" style="font-size: 40px;"></nb-icon>
                </div>
                <div class="title-row mt-2 pb-0">
                  <p class="fs-5">Nombre Factures Pénalités</p>
                </div>
                <div class="stats-row align-self-end justify-content-start pb-0">
                  <h4>0</h4>
                </div>
              </div>
            </div>
          </div>
          <!-- Card 3 -->
          <div class="col-lg-2 my-5">
            <div class="card small-cards shadow mb-2 bg-white">
              <div class="card-body d-flex flex-column">
                <div class="icon-row4 d-flex justify-content-center align-items-center shadow">
                  <nb-icon icon="check" pack="fas" style="font-size: 40px;"></nb-icon>
                </div>
                <div class="title-row mt-2 pb-0">
                  <p class="fs-5">Volumne Factures (MAD)</p>
                </div>
                <div class="stats-row align-self-end justify-content-start pb-0">
                  <h4>3</h4>
                </div>
              </div>
            </div>
          </div>
          <!-- Card 4 -->
          <div class="col-lg-2 my-5">
            <div class="card small-cards shadow mb-5 bg-white">
              <div class="card-body d-flex flex-column">
                <div class="icon-row2 d-flex justify-content-center align-items-center shadow">
                  <nb-icon icon="money-bill" pack="fas" style="font-size: 40px;"></nb-icon>
                </div>
                <div class="title-row mt-2 pb-0">
                  <p class="fs-5">Budget</p>
                </div>
                <div class="stats-row align-self-end justify-content-start pb-0">
                  <h4>177,690.6</h4>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div id="divBarChart" class="mt-4 col-lg-6" style="padding:2%">
          <highcharts-chart
            [Highcharts]="Highcharts"
            [options]="barChartOptions"
            style="width: 100%; height: 400px; display: block;">
          </highcharts-chart>
        </div>
      </nb-card-body>
    </ng-container>
  </ng-container>
</nb-card>
