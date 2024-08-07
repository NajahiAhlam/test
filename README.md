createPieChart(data: any): void {
  if (!data || typeof data !== 'object') {
    console.error('Invalid data for pie chart:', data);
    return;
  }

  // Convert the object properties into an array of [name, value]
  const seriesData = Object.entries(data)
    .map(([key, value]) => ({ name: key, y: value }))
    .filter(item => item.y > 0); // Filter out items with zero values if needed

  if (seriesData.length === 0) {
    console.error('No valid data available for pie chart.');
    return;
  }

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

  // Ensure that the chart container is available
  const chartContainer = document.getElementById('pie-chart-container');
  if (chartContainer) {
    Highcharts.chart('pie-chart-container', this.pieChartOptions);
  } else {
    console.error('Chart container not found.');
  }
}
