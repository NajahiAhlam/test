hey i want in front to show the fournisseurRaisonSociale to show in header and price under like we did in article
export class ArticleListComponent implements OnInit{
  @Input() projetId!: number
  filters: any = {};
  data$: ObjectPagination<Article> | undefined;
  errorMessage!: string;
  readonly DataStateEnum = DataStateEnum;
  elementPerPage = 10;
  pageNumber = 0;
  sortDirection = false;
  sortValue: string = "";
  positions = NbGlobalPhysicalPosition;
  form: any = {};
  @Input() showFournisseurPrix: boolean = true;
  @ViewChild(DynamicTableBackManagementComponent) table!: DynamicTableBackManagementComponent;
  loading: boolean = false;
  fournisseurHeaders: string[] = [];
  //columns: any[]=[];
  columns = [
    { key: 'code', label: 'Code' },
    { key: 'designation', label: 'Désignation' },
    { key: 'unite', label: 'Unité' },
    { key: 'typeArticle', label: 'Type' },
   
  ];

  config: { [key: string]: any } = {};
  filtre!: string;

   constructor(
    private articleService: GestionArticleService,
    private _utilService: UtilsService,
    private windowService: NbDialogService,
    private router: Router,
    private cdr: ChangeDetectorRef,
    private csvService: ExportCSVService
  ) {}
 

  
  ngOnInit(): void {
     this.filters = { projetId: this.projetId };
    this.getAllArticles(this.filters)
  }

 
   onPageNumberChange($event: number) {
    this.pageNumber = $event;
    this.getAllArticles(this.form);
  }

  onElementPerPageChange($event: number) {
    this.elementPerPage = $event;
    this.getAllArticles(this.form);
  }

  sortBy($event: any) {
    this.sortValue = $event;
    this.getAllArticles(this.form);
  }

  onSearch($event: any) {
    this.getAllArticles($event);
  }



  getAllArticles(searchCriteria?: any): void {
    try {
      this.form = searchCriteria || {};
      this.loading = true;

      const formattedCriteria = { ...this.form };

      this.articleService.searchRequest(
        this.pageNumber,
        this.elementPerPage,
        this.sortValue,
        this.sortDirection ? 'ASC' : 'DESC',
        formattedCriteria
      ).subscribe({
       next: data => {
  if (data?.content?.length) {
    const firstArticle = data.content[0];

    // Collect all unique fournisseur names
    const fournisseurs = new Set<string>();
    data.content.forEach(article => {
      article.priceArticles?.forEach((pa: any) => {
        if (pa.fournisseur?.raisonSociale) {
          fournisseurs.add(pa.fournisseur.raisonSociale);
        }
      });
    });

    this.fournisseurHeaders = Array.from(fournisseurs);

    // Build columns dynamically
    this.columns = [
      { key: 'code', label: 'Code' },
      { key: 'designation', label: 'Désignation' },
      { key: 'unite', label: 'Unité' },
      { key: 'typeArticle', label: 'Type' },
      ...this.fournisseurHeaders.map(f => ({
        key: f,
        label: f,
        render: (row: any) => {
          const pa = row.priceArticles?.find(
            (p: any) => p.fournisseur?.raisonSociale === f
          );
          return pa ? pa.price : '-';
        }
      }))
    ];
  }

  this.data$ = data;
  this.loading = false;
},
        error: err => {
          console.error(err);
          this.loading = false;
          this._utilService.displayError('Une erreur technique est survenue', "Erreur");
        }
      });
    } catch (error) {
      console.error(error);
      this.loading = false;
      this._utilService.displayError('Une erreur inattendue est survenue', "Erreur");
    }
  }

  getAllArticlesS(searchCriteria?: any): void {
  try {
    this.form = searchCriteria || {};
    this.loading = true;
    searchCriteria.projetId = this.projetId;

    const formattedCriteria = { ...this.form, projetId: this.projetId };

    this.articleService.searchRequest(
      this.pageNumber,
      this.elementPerPage,
      this.sortValue,
      this.sortDirection ? 'ASC' : 'DESC',
      formattedCriteria
    ).subscribe({
      next: data => {
        if (!data.content.length) {
          this.loading = false;
          this.data$ = data;
          return;
        }

        if (this.pageNumber >= data.totalPages && data.content.length > 0) {
          this.pageNumber = 0;
          this.getAllArticles(formattedCriteria);
        } else {
          this.data$ = data;
        }

        this.loading = false;
      },
      error: err => {
        console.error(err);
        this.loading = false;
        this._utilService.displayError('Une erreur technique est survenue', "Erreur");
      }
    });
  } catch (error) {
    console.error(error);
    this.loading = false;
    this._utilService.displayError('Une erreur inattendue est survenue', "Erreur");
  }
}


  onSortDirectionChange($event: any) {
    this.sortDirection = $event;
  }




  exportt() {
  const totalElements = this.data$?.totalElements!;
  const maxPerFile = 100;
  const totalParts = Math.ceil(totalElements / maxPerFile);

  this.loading = true;
  const formattedCriteria = { ...this.form, projetId: this.projetId };


  const exportPart = (partNumber: number) => {
    const pageSize = maxPerFile;
    const page = partNumber;

    this.articleService.searchRequest(page, pageSize, this.sortValue, this.sortDirection ? 'ASC' : 'DESC',formattedCriteria).subscribe({
      next: (data) => {
        this.csvService.exportToCsv(
          this.formatCsvData(data.content),
        `Liste d'articles – Partie ${partNumber + 1}`

        );

        if (partNumber + 1 < totalParts) {
          exportPart(partNumber + 1);
        } else {
          this.loading = false;
        }
      },
      error: (err) => {
        this.loading = false;
        this._utilService.displayError('Une erreur technique est survenue', 'Erreur');
      }
    });
  };

  exportPart(0);
}
export() {
  if (!this.data$?.totalElements) {
    this._utilService.displayError('Aucune donnée à exporter', 'Erreur');
    return;
  }

  this.loading = true;
  const formattedCriteria = { ...this.form, projetId: this.projetId };

  const pageSize = this.data$.totalElements; 

  this.articleService
    .searchRequest(0, pageSize, this.sortValue, this.sortDirection ? 'ASC' : 'DESC', formattedCriteria)
    .subscribe({
      next: (data) => {
        this.csvService.exportToCsv(
        this.formatCsvData(data.content),
        `Liste d'articles`
        );
        this.loading = false;
      },
      error: (err) => {
        console.error(err);
        this.loading = false;
        this._utilService.displayError('Une erreur technique est survenue', 'Erreur');
      },
    });
}


  formatCsvData(data: any[]): any[] {
  return (data || []).map(item => {
    const base: any = {
      'Code': item.code,
      'Désignation': item.designation,
      'Unité': item.unite,
      'Type': item.typeArticle,
    };

    this.fournisseurHeaders.forEach(f => {
      const pa = item.priceArticles?.find(
        (p: any) => p.fournisseur?.raisonSociale === f
      );
      base[f] = pa ? pa.price : '';
    });

    return base;
  });
  }


  import() {
  this.windowService
    .open(ArticleInjectionFormComponent, {})
    .onClose.subscribe((data) => {
      if (data === 'SUCCESS') {
        this.columns = [];
        this.data$ = undefined;
        setTimeout(() => {
          this.getAllArticles();
          this.cdr.detectChanges();
        }, 300);
      }
    });
}



}
