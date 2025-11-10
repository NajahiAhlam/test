export class ArticleListComponent implements OnInit {
  @Input() projetId!: number;
  data$: ObjectPagination<Article> | undefined;

  showFournisseurPrix: boolean = false; // controls the column
  columns: any[] = [];

  ngOnInit(): void {
    this.initColumns();
    // load articles
    this.getAllArticles({ projetId: this.projetId });
  }

  // Initialize columns
  initColumns() {
    this.columns = [
      { key: 'code', label: 'Code' },
      { key: 'designation', label: 'Désignation' },
      { key: 'unite', label: 'Unité' },
      { key: 'quantite', label: 'Quantité' },
      { key: 'typeArticle', label: 'Type' },
      ...(this.showFournisseurPrix ? [{
        key: 'priceArticles',
        label: 'Fournisseurs et prix',
        render: (row: any) => {
          if (!row.priceArticles || row.priceArticles.length === 0) return '-';
          return row.priceArticles
            .map((pa: any) => `${pa.fournisseur?.raisonSociale}: ${pa.price}`)
            .join(', ');
        }
      }] : [])
    ];
  }

  // Called whenever stepper changes step
  onStepChange(stepIndex: number) {
    // Show fournisseur & price only on step 2 (index 1)
    this.showFournisseurPrix = (stepIndex === 1);
    this.initColumns(); // update columns dynamically
  }
}
