export class PilotageConditionsComponent {
   formSubscription?: Subscription;
  readonly DataStateEnum = DataStateEnum;
  loading = false
  maxDateRange!: Date;
  periodRange: 'dd' | 'MM' | 'quarter' | 'yyyy' = 'MM';
  dateFrom = new Date();
  dateTo = new Date();
  datePreFrom = new Date();
  datePreTo = new Date();
  currentFiltreByStatus: string = '';
  startDate: string = '';
  endDate: string = '';
  rawGroupedData: any = {};
  tableData: any[] = [];
  filteredData: any[] = [];
  columnss: string[] = ['assigneeName', 'risqueName', 'validateur', 'productNom', 'productCode', 'productStatus', 'dateLancement', 'NbrS', 'dateEcheance', 'categorie'];
  filterText: string = '';
  columnFilters: { [key: string]: string } = {};
  
 columns = [
    { key: 'assigneeName', label: 'Assigne' },
    { key: 'risqueName', label: 'Risque' },
    { key: 'validateur', label: 'Responsable' },
    { key: 'productNom', label: 'Nom du produit' },
    { key: 'productCode', label: 'Code produit' },
    { key: 'productStatus', label: 'Satut' },
    //{ key: 'dateLancement', label: 'Date Lancement' },
    //{ key: 'NbrS', label: 'Nombre Semaine Apres lancement' },
    { key: 'dateRevenue', label: 'Date Revenue' },
    { key: 'categorie', label: 'Categorie' },
    { key: 'etatCondition', label: 'Etat Condition' },
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
    this.maxDateRange = this.dateService.addDay(this.dateService.today(), 0);
   this.analyticService.getReportConditionByAssigne().subscribe(
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



    resetAllValues() {
    this.dateTo = new Date();
    this.dateFrom = new Date();
    this.datePreFrom = new Date();
    this.datePreTo = new Date();
  }

  fetchByRange(event: NbCalendarRange<Date>) {
    this.resetAllValues();
    if (event.start && event.end) {
      this.dateFrom = event.start;
      let endDate = new Date(event.end);
      endDate.setHours(23, 59, 59);
      this.dateTo = endDate

      this.forkJoinData();
    }
  }
   forkJoinData() {
    const dateDemandePrevFrom = this.utilsService.formatDate(this.datePreFrom, "yyyy-MM-dd'T'HH:mm:ss");
    const dateDemandePrevTo = this.utilsService.formatDate(this.datePreTo, "yyyy-MM-dd'T'HH:mm:ss");
    const dateDemandeFrom = this.utilsService.formatDate(this.dateFrom, "yyyy-MM-dd'T'HH:mm:ss");
    const dateDemandeTo = this.utilsService.formatDate(this.dateTo, "yyyy-MM-dd'T'HH:mm:ss");


    //this.getAnalytics(dateDemandePrevFrom, dateDemandePrevTo, dateDemandeFrom, dateDemandeTo)
  }
 
}
<div id="auditPilotage">
  <div class="pilotageHeader d-flex justify-content-between">
    <div class="pilotageTitle">
      <h4 class="title-heading  text-uppercase"></h4>
    </div>
    <div class="paginationFilter d-flex">
      <div class="control-status-example">
      </div>
      <div class="rangeCalendar ml-3"
           title="vs {{datePreFrom|date:'dd/MM/yyyy'}} - {{datePreTo|date:'dd/MM/yyyy'}}">
        <input class="form-control" placeholder="{{dateFrom|date:'shortDate'}} - {{dateTo|date:'shortDate'}} "
               [nbDatepicker]="rangepicker" size="21" />

        <nb-rangepicker (rangeChange)=fetchByRange($event) format="dd/MM/yyyy" date #rangepicker
                        [max]="maxDateRange"></nb-rangepicker>
      </div>
    </div>
  </div>
</div>
<nb-card>
  <nb-card-header>Liste des Conditions 

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
