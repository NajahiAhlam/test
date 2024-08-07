   <nb-card>
        <nb-card-header>Project Phase Counts</nb-card-header>
        <nb-card-body>
          <nb-list class="container">
            <nb-list-item>
              <nb-icon icon="nb-compose"></nb-icon>
              <span class="data-label py-2 col-4">Non Lances:</span>
              <h4 class="data-value py-2 col-7">{{ aggregatedDataa?.NonLances || 0 }}</h4>
            </nb-list-item>
            <nb-list-item>
              <nb-icon icon="nb-reload"></nb-icon>
              <span class="data-label py-2 col-4">En Cadrage:</span>
              <h4 class="data-value py-2 col-7">{{ aggregatedDataa?.EnCadrage || 0 }}</h4>
            </nb-list-item>
            <nb-list-item>
              <nb-icon icon="nb-bar-chart"></nb-icon>
              <span class="data-label py-2 col-4">En Realisation:</span>
              <h4 class="data-value py-2 col-7">{{ aggregatedDataa?.EnRealisation || 0 }}</h4>
            </nb-list-item>
            <nb-list-item>
              <nb-icon icon="nb-arrow-up"></nb-icon>
              <span class="data-label py-2 col-4">Preclotures:</span>
              <h4 class="data-value py-2 col-7">{{ aggregatedDataa?.Preclotures || 0 }}</h4>
            </nb-list-item>
            <nb-list-item>
              <nb-icon icon="nb-arrow-down"></nb-icon>
              <span class="data-label py-2 col-4">Clotures:</span>
              <h4 class="data-value py-2 col-7">{{ aggregatedDataa?.Clotures || 0 }}</h4>
            </nb-list-item>
            <nb-list-item>
              <nb-icon icon="nb-cloud"></nb-icon>
              <span class="data-label py-2 col-4">Abandonnees:</span>
              <h4 class="data-value py-2 col-7">{{ aggregatedDataa?.Abandonnees || 0 }}</h4>
            </nb-list-item>
            <nb-list-item>
              <nb-icon icon="nb-signal"></nb-icon>
              <span class="data-label py-2 col-4">Total Projects:</span>
              <h4 class="data-value py-2 col-7">{{ aggregatedDataa?.countProjet || 0 }}</h4>
            </nb-list-item>
          </nb-list>
        </nb-card-body>
      </nb-card>
        <nb-card>
            <nb-card-header>
              <div>
                Répartition par Type Agile
              </div>
            </nb-card-header>
            <nb-card-body>
                <table class="table w-100">
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
            </nb-card-body>
          </nb-card>
          i want to add icon on this card when i click on it to show a pie chart i give you a pie chart on how i want the new to be :
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
             // fontWeight: 'bold',
              color: 'black'
            }
          }
        }
      },
      credits: { enabled: false } 
    };
  
    Highcharts.chart('pie-chart-container', this.pieChartOptions);
  }
  i want to inside show the countProjet
    this.analyticsService.getAggregatedDataa(this.selectedYear, this.selectedProgramme, this.selectedLead).subscribe(data => {
      this.aggregatedDataa = data;
    });
