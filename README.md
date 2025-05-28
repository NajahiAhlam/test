 columns = [
   { key: 'objetDemande', label: 'Titre' },
   { key: 'nomFiliale', label: 'Filiale' },
   { key: 'type', label: 'Type' },
   { key: 'dateCreation', label: 'Date de création' },
   { key: 'description', label: 'Description' },
   { key: 'dateLancement', label: 'Date de Lancement' },
   { key: 'porteurProjet', label: 'Porteur Projet' },
   { key: 'dateModification', label: 'Date de Modification' },
   { key: 'porteurMetier', label: 'Porteur Metier' },
   { key: 'status', label: 'Statut' },
   { key: 'dateKickOff', label: 'Date de KickOff' },
   { key: 'kickOffDecision', label: 'Decision kickOff' },
    { key: 'typeDemande', label: 'Type Demande' },
   
 ];
hey i want a do like this and in affichage show the label but in filter and all use the key
  this.analyticService.getReportByDate(this.dateFrom,this.dateTo).subscribe(
      (data) => {
        this.reports = data;
        console.log('Report Data:', this.reports);
      },
      (error) => {
        console.error('Error fetching reports:', error);
      }
    );

 

   this.analyticService.getReportConditionByAssigne().subscribe(
      (data) => {
        const typedData = data as Record<string, any[]>;
        this.rawGroupedData = typedData;
        this.tableData = this.flattenGroupedData(typedData);
        this.filteredData = [...this.tableData];
        this.initializeFilters();
      },
      (error) => {
        console.error('Error fetching reports:', error);
      }
    );
  }


flattenGroupedData(data: Record<string, any[]>): any[] {
    return Object.entries(data).flatMap(([assigneeName, items]) =>
      items.map(item => ({ ...item, assigneeName }))
    );
  }

 initializeFilters(): void {
  this.columnFilters = {};
  this.columns.forEach(col => this.columnFilters[col] = '');
}
 onColumnFilterChange(): void {
  this.filteredData = this.tableData.filter(row =>
    this.columns.every(col => {
      const filterValue = this.columnFilters[col].toLowerCase();
      const rowValue = (row[col] ?? '').toString().toLowerCase();
      return rowValue.includes(filterValue);
    })
  );
}
