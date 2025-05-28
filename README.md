{
    "jean.dupont@example.com": [
        {
            "dateLancement": null,
            "productNom": "test",
            "NbrS": null,
            "productCode": null,
            "categorie": "précondition",
            "validateur": "laura.faure@example.com",
            "risqueName": "Risques de crédit ",
            "productStatus": "CLOTURE",
            "dateEcheance": "2025-05-05"
        },
        {
            "dateLancement": "2025-05-11",
            "productNom": "test",
            "NbrS": 2,
            "productCode": null,
            "categorie": "poste condition",
            "validateur": "laura.faure@example.com",
            "risqueName": "Risques de crédit ",
            "productStatus": "CLOTURE",
            "dateEcheance": null
        }
    ],
    "luc.lefevre@example.com": [
        {
            "dateLancement": null,
            "productNom": "test",
            "NbrS": null,
            "productCode": null,
            "categorie": "précondition",
            "validateur": "pierre.lambert@example.com",
            "risqueName": "Risques de marché",
            "productStatus": "CLOTURE",
            "dateEcheance": "2025-05-05"
        }
    ]
}
<table class="table w-100">
  <thead class="bg-header fw-bold">
    <tr>
      <th *ngFor="let col of columns">{{ col.label | titlecase }}</th>
    </tr>
    <tr>
      <th *ngFor="let col of columns">
        <input
          nbInput
          fullWidth
          class="form-control"
          [(ngModel)]="columnFilters[col.key]"
          (ngModelChange)="onColumnFilterChange()"
          placeholder="{{ col.label }}"
        />
      </th>
    </tr>
  </thead>
  <tbody>
    <tr *ngFor="let row of filteredData">
      <td *ngFor="let col of columns">
         <ng-container [ngSwitch]="col.key">

    <!-- Handle 'validateur' as special case -->
    <ng-container *ngSwitchCase="'validateur'">
      {{ row[col.key] | mail }} 
    </ng-container>

    <ng-container *ngSwitchCase="'assigneeName'">
      {{ row[col.key] | mail }} 
    </ng-container>
    <ng-container *ngSwitchCase="'categorie'">
      {{getCategorieClass(row[col.key]) }} 
    </ng-container>

       <ng-container *ngSwitchCase="'productStatus'">
      {{getActionLabel(row[col.key]) }} 
    </ng-container>

    <!-- Default case -->
    <ng-container *ngSwitchDefault>
      {{ row[col.key] }}
    </ng-container>

  </ng-container>
      </td>
    </tr>
  </tbody>
</table>
  rawGroupedData: any = {};
  tableData: any[] = [];
  filteredData: any[] = [];
  filterText: string = '';
  columnFilters: { [key: string]: string } = {};
  
 columns = [
    { key: 'assigneeName', label: 'Assigne' },
    { key: 'risqueName', label: 'Risque' },
    { key: 'validateur', label: 'Responsable' },
    { key: 'productNom', label: 'Nom du produit' },
    { key: 'productCode', label: 'Code produit' },
    { key: 'productStatus', label: 'Satut' },
    { key: 'dateLancement', label: 'Date Lancement' },
    { key: 'NbrS', label: 'Nombre Semaine Apres lancement' },
    { key: 'dateEcheance', label: 'Date Echeance' },
    { key: 'categorie', label: 'Categorie' },
  ];
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



  flattenGroupedData(data: Record<string, any[]>): any[] {
    return Object.entries(data).flatMap(([assigneeName, items]) =>
      items.map(item => ({ ...item, assigneeName }))
    );
  }

  initializeFilters(): void {
    this.columnFilters = {};
    this.columns.forEach(col => this.columnFilters[col.key] = '');
  }

  onColumnFilterChange(): void {
    this.filteredData = this.tableData.filter(row =>
      this.columns.every(col => {
        const filterValue = this.columnFilters[col.key].toLowerCase();
        const rowValue = (row[col.key] ?? '').toString().toLowerCase();
        return rowValue.includes(filterValue);
      })
    );
  }
  HEY AS you see i have by Assigne name came list in table the colonne that show assigneName it duplicate i want to rowspan the colonne and somme all ligne that have 
