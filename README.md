<div id="auditPilotage">
  <div class="pilotageHeader d-flex justify-content-between">
    <div class="pilotageTitle">
      <h4 class="title-heading  text-uppercase"></h4>
    </div>
    <div class="paginationFilter d-flex">
      <div class="control-status-example">
      </div>


    <div class="rangeCalendar ml-3"
     title="vs {{ datePreFrom | date:'dd/MM/yyyy' }} - {{ datePreTo | date:'dd/MM/yyyy' }}">
  <input class="form-control"
         placeholder="{{ datePreFrom | date:'shortDate' }} - {{ datePreTo | date:'shortDate' }}"
         [nbDatepicker]="rangepicker"
         size="21" />

  <nb-rangepicker #rangepicker
                  [range]="{ start: datePreFrom, end: datePreTo }"
                  [max]="maxDateRange"
                  format="dd/MM/yyyy"
                  (rangeChange)="fetchByRange($event)">
  </nb-rangepicker>
</div>
    </div>
  </div>




</div>

<nb-card>
  <nb-card-header>Liste des Produits / Conditions 

      <div class="d-flex flex-row justify-content-end">
              <button type="submit" class="d-flex btn custom-bg m-1"
                       (click)="exportToCSV()">
                exporter csv
              </button>

            </div>
  </nb-card-header>

  <nb-card-body>

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

    <ng-container *ngSwitchDefault>
      {{ row[col.key] }}
    </ng-container>

  </ng-container>
      </td>
    </tr>
  </tbody>
</table> 
  <p *ngIf="filteredData.length === 0" class="center-text">Aucune donnée disponible pour ces filtres.</p>
  </nb-card-body>
</nb-card>
export class PilotageConditionsComponent {
  rawGroupedData: any = {};
  tableData: any[] = [];
  filteredData: any[] = [];
  columnss: string[] = ['assigneeName', 'risqueName', 'validateur', 'productNom', 'productCode', 'productStatus', 'dateLancement', 'NbrS', 'dateEcheance', 'categorie'];
  filterText: string = '';
  columnFilters: { [key: string]: string } = {};
    datePreFrom!: Date;
  datePreTo!: Date;
  maxDateRange = new Date();
  
 columns = [
  { key: 'assigneeName', label: 'Assigné' },
{ key: 'risqueName', label: 'Risque' },
{ key: 'validateur', label: 'Assignant' },
{ key: 'productNom', label: 'Nom du produit' },
{ key: 'productCode', label: 'Code du produit' },
{ key: 'productStatus', label: 'Statut' },
{ key: 'dateRevenue', label: 'Date de revenu' },
{ key: 'categorie', label: 'Catégorie' },
{ key: 'etatCondition', label: 'État de la condition' }

  ];
  
  groupedReports: any[] = [];

  constructor(
    protected dateService: NbDateService<Date>,
    private utilsService: UtilsService,
    private analyticService: AnalyticService,
    @Inject(DOCUMENT) public document: Document
  ) {
    this.maxDateRange = this.dateService.addDay(this.dateService.today(), 0);

  }
   ngOnInit(): void {
   
 const now = new Date();
    const firstDayPrevMonth = new Date(now.getFullYear(), now.getMonth() - 1, 1);
    const lastDayPrevMonth = new Date(now.getFullYear(), now.getMonth(), 0); 

    this.datePreFrom = firstDayPrevMonth;
    this.datePreTo = lastDayPrevMonth;

  this.fetchReportByDateRange(this.datePreFrom, this.datePreTo)

  }

fetchByRange(range: NbCalendarRange<Date>): void {
  if (range.start && range.end) {
    this.fetchReportByDateRange(range.start, range.end);
  }
}

 fetchReportByDateRange(start: Date, end: Date): void {
    console.log("************start:", start)
    console.log("************end:", end)
    this.datePreFrom = start;
    this.datePreTo = end;

    this.analyticService.getReportConditionByAssigne(start, end).subscribe(
      (data) => {
        const typedData = data as Record<string, any[]>;
        this.rawGroupedData = typedData;
        this.tableData = this.flattenGroupedData(typedData);
        this.filteredData = [...this.tableData];
        this.initializeFilters();
        this.buildGroupedRows();
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
    this.columns.forEach(col => this.columnFilters[col.key] = '');
  }

  onColumnFilterChangeold(): void {
    this.filteredData = this.tableData.filter(row =>
      this.columns.every(col => {
        const filterValue = this.columnFilters[col.key].toLowerCase();
        const rowValue = (row[col.key] ?? '').toString().toLowerCase();
        return rowValue.includes(filterValue);
      })
    );
  }
groupedRows: {
  assigneeName: string;
  rows: any[];
  rowspan: number;
  sumNbrS: number;
}[] = [];

buildGroupedRows() {
  const map = new Map<string, any[]>();

  for (const row of this.filteredData) {
    const key = row.assigneeName ?? 'Unknown';
    if (!map.has(key)) {
      map.set(key, []);
    }
    map.get(key)!.push(row);
  }

  this.groupedRows = [];
  map.forEach((rows, assigneeName) => {
    const sumNbrS = rows.reduce((acc, r) => acc + (Number(r.NbrS) || 0), 0);
    this.groupedRows.push({
      assigneeName,
      rows,
      rowspan: rows.length,
      sumNbrS
    });
  });
}
onColumnFilterChange(): void {
  this.filteredData = this.tableData.filter(row =>
    this.columns.every(col => {
      const filterValue = this.columnFilters[col.key].toLowerCase();
      const rowValue = (row[col.key] ?? '').toString().toLowerCase();
      return rowValue.includes(filterValue);
    })
  );
  this.buildGroupedRows();
}


}








