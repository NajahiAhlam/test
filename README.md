createPieChart(data: any[]): void {
    if (!data || !Array.isArray(data)) {
      console.error('Invalid data for pie chart:', data);
      return;
    }
  
    const seriesData = data.map(item => ({
      name: item[0],
      y: Number(item[1])  // Ensure y is a number
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
