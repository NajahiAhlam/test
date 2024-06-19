import { AfterViewInit, Component } from '@angular/core';
import * as Highcharts from 'highcharts';

@Component({
  selector: 'app-chart-example',
  templateUrl: './chart-example.component.html',
  styleUrls: ['./chart-example.component.css']
})
export class ChartExampleComponent implements AfterViewInit {
  Highcharts: typeof Highcharts = Highcharts;
  chart: Highcharts.Chart | undefined;

  chartOptions: Highcharts.Options = {
    chart: {
      type: 'column'
    },
    title: {
      text: "Born persons, by girls' name"
    },
    subtitle: {
      text: 'Resize the frame or click buttons to change appearance'
    },
    legend: {
      align: 'right',
      verticalAlign: 'middle',
      layout: 'vertical'
    },
    xAxis: {
      categories: ['2019', '2020', '2021'],
      labels: {
        x: -10
      }
    },
    yAxis: {
      allowDecimals: false,
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
    }],
    responsive: {
      rules: [{
        condition: {
          maxWidth: 500
        },
        chartOptions: {
          legend: {
            align: 'center',
            verticalAlign: 'bottom',
            layout: 'horizontal'
          },
          yAxis: {
            labels: {
              align: 'left',
              x: 0,
              y: -5
            },
            title: {
              text: null
            }
          },
          subtitle: {
            text: null
          },
          credits: {
            enabled: false
          }
        }
      }]
    }
  };

  constructor() { }

  ngAfterViewInit(): void {
    this.chart = Highcharts.chart('container', this.chartOptions);
  }

  setChartSize(size: number | null): void {
    if (this.chart) {
      this.chart.setSize(size);
    }
  }
}
