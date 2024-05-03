<nb-card>
    <nb-card-header class="d-flex justify-content-between mb-4">
      <div >
        <h5>Date Harmonization Report</h5>
      </div>
      
    </nb-card-header>
  
  </nb-card>

      <section  class="row d-flex justify-content-between">
        <section class="col-lg-6">
            <nb-card>
                <nb-card-header>
                      <h5 class="fs-5">Situation Budget Globale par Etat </h5>
                </nb-card-header>
                <nb-card-body class="d-flex" >  
                  <div id="divBarChart" class="mt-4 col-lg-12" >
                  <canvas #barChart id="barChart" style=" position: relative;"></canvas>
                </div>  
                </nb-card-body>
            </nb-card>
        </section>
        <section class="col-lg-6">
            <nb-card>
                <nb-card-header>
                    <h5 class="fs-5">P2P vs Cockpit </h5>
                </nb-card-header>
                <nb-card-body class="d-flex" >  
                  <div id="divBarChart" class="mt-4 col-lg-12" style="padding:2%">
                  <canvas #barChart2 id="barChart2" style=" position: relative;"></canvas>
                </div>  
                </nb-card-body>
            </nb-card>
        </section>
      </section>


      <section  class="row d-flex justify-content-between">
        <section class="col-lg-6">
            <nb-card>
                <nb-card-header>
                    <h5 class="fs-5">CockPit vs Sciforma-COOTech </h5>
                </nb-card-header>
                <nb-card-body class="d-flex" >  
                  <div id="divBarChart" class="mt-4 col-lg-12" style="padding:2%">
                  <canvas #barChart3 id="barChart4" style=" position: relative;"></canvas>
                </div>  
                </nb-card-body>
            </nb-card>
        </section>
        <section class="col-lg-6">
            <nb-card>
                <nb-card-header>
                    <h5 class="fs-5">CockPit vs Sciforma-SGABS </h5>
                </nb-card-header>
                <nb-card-body class="d-flex" >  
                  <div id="divBarChart" class="mt-4 col-lg-12" style="padding:2%">
                  <canvas #barChart4 id="barChart4" style=" position: relative;"></canvas>
                </div>  
                </nb-card-body>
            </nb-card>
        </section>
      </section>
<nb-tabset>

       ------------------------------------------------------------------------------------------------------------

       import { Component, ElementRef, OnInit, ViewChild } from '@angular/core';
import { Chart } from 'chart.js';

@Component({
  selector: 'app-dhr',
  templateUrl: './dhr.component.html',
  styleUrls: ['./dhr.component.scss']
})
export class DHRComponent implements OnInit {
  @ViewChild('barChart') barChartCanvas!: ElementRef<HTMLCanvasElement>;
  @ViewChild('barChart2') barChartCanvas2!: ElementRef<HTMLCanvasElement>;
  @ViewChild('barChart3') barChartCanvas3!: ElementRef<HTMLCanvasElement>;
  @ViewChild('barChart4') barChartCanvas4!: ElementRef<HTMLCanvasElement>;

  constructor() { }

  ngOnInit(): void {
  }
  ngAfterViewInit(): void {
    this.createDonutChart1()
    this.createDonutChart2()
    this.createDonutChart3()
    this.createDonutChart4()
  }
  createDonutChart1(){
    const ctx = this.barChartCanvas.nativeElement;
    new Chart(ctx, {
      type: 'bar',
      data: {
        labels: ['Initial', 'Up To Date', 'Atterissage'],
        datasets: [{
          label: '# Etat',
          data: [281, 281, 0],
          backgroundColor: [
            'rgb(255,61,113,1)',
            'rgba(57, 29, 242)',
            'rgba(44,230,155,1)'
          ],
          borderColor: [
            'rgb(255,61,113,1)',
            'rgba(57, 29, 242)',
            'rgba(44,230,155,1)'
          ],
          borderWidth: 1
        }]
      },
      options: {
        plugins: {
          legend: {
            display : true

          }},
        scales: {
          y: {
            display: true
          },
          x: {
            display: true
          }
        }
      }
    });
  }

  createDonutChart2(){
    const ctx = this.barChartCanvas2.nativeElement;
    new Chart(ctx, {
      type: 'bar',
      data: {
        labels: ['Engagé >> Alloué', 'Consommé >> Engagé'],
        datasets: [{
          label: '# # Nombre de Projet en écrat Budget CTB',
          data: [281, 281, 0],
          backgroundColor: [
            'rgb(255,61,113,1)',
            'rgba(44,230,155,1)'
          ],
          borderColor: [
            'rgb(255,61,113,1)',
            'rgba(44,230,155,1)'
          ],
          borderWidth: 1
        }]
      },
      options: {
        plugins: {
          legend: {
            display : true

          }},
        scales: {
          y: {
            display: true
          },
          x: {
            display: true
          }
        }
      }
    });
  }

  createDonutChart3(){
    const ctx = this.barChartCanvas3.nativeElement;
    new Chart(ctx, {
      type: 'bar',
      data: {
        labels: ['Alloué CockPit # Alloué Sciforma', 'Consommé Sciforma >> Alloué Sciforma'],
        datasets: [{
          label: '# Nombre de Projet en écrat Budget Jh',
          data: [88, 7, 0],
          backgroundColor: [
            'rgb(255,61,113,1)',
            'rgba(57, 29, 242)'
          ],
          borderColor: [
            'rgb(255,61,113,1)',
            'rgba(57, 29, 242)'
          ],
          borderWidth: 1
        }]
      },
      options: {
        plugins: {
          legend: {
            display : true

          }},
        scales: {
          y: {
            display: true
          },
          x: {
            display: true
          }
        }
      }
    });
  }

  createDonutChart4(){
    const ctx = this.barChartCanvas4.nativeElement;
    new Chart(ctx, {
      type: 'bar',
      data: {
        labels: ['Alloué CockPit # Alloué Sciforma', 'Consommé Sciforma >> Alloué Sciforma'],
        datasets: [{
          label: '# Nombre de Projet en écrat Budget Jh',
          data: [55, 29],
          backgroundColor: [
            'rgb(255,61,113,1)',
            'rgba(44,230,155,1)'
          ],
          borderColor: [
            'rgb(255,61,113,1)',
            'rgba(44,230,155,1)'
          ],
          borderWidth: 1
        }]
      },
      options: {
        plugins: {
          legend: {
            display : true

          }},
        scales: {
          y: {
            display: true
          },
          x: {
            display: true
          }
        }
      }
    });
  }


}
