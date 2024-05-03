 showDetails(chart: Chart, event: MouseEvent) {
    const activeElement = chart.getElementsAtEventForMode(event, 'nearest', { intersect: true }, true)[0] as ChartElement;
    if (activeElement) {
      // Extract label and data
      const label = chart.data.labels[activeElement.index];
      const data = chart.data.datasets[activeElement.datasetIndex].data[activeElement.index];

      // Display details in a table or perform any other action
      console.log(`Clicked: ${label}, Data: ${data}`);
    }
  }
