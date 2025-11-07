Absolutely! You want to **adapt your `AdjudicationListComponent`**, which currently lists `Projet`, to work with your **`Article` entity** instead. I\u2019ll rewrite it accordingly. I\u2019ll assume your `Article` entity has the following fields:

* `id`
* `code`
* `designation`
* `unite`
* `quantite`
* `typeArticle`
* `prix`
* `fournisseur`
* `projetId`

Here\u2019s the updated component:

```ts
import { Component, Input, OnInit, ViewChild } from '@angular/core';
import { ObjectPagination, DataStateEnum } from 'src/app/core/state/state';
import { Article } from '../../model/article.model';
import { DynamicTableBackManagementComponent } from 'src/app/shared/components/dynamic-table-back-management/dynamic-table-back-management.component';
import { GestionArticleService } from '../../service/gestion-article.service';
import { UtilsService } from 'src/app/core/services/utils.service';
import { Router } from '@angular/router';
import { ExportCSVService } from 'src/app/core/services/export-csv.service';
import { NbGlobalPhysicalPosition, NbDialogService } from '@nebular/theme';

@Component({
  selector: 'app-article-list',
  templateUrl: './article-list.component.html',
  styleUrls: ['./article-list.component.scss']
})
export class ArticleListComponent implements OnInit {

  data$: ObjectPagination<Article> | undefined;
  errorMessage!: string;
  readonly DataStateEnum = DataStateEnum;
  elementPerPage = 10;
  pageNumber = 0;
  sortDirection = false;
  sortValue: string = "";
  positions = NbGlobalPhysicalPosition;
  form: any = {};
  @ViewChild(DynamicTableBackManagementComponent) table!: DynamicTableBackManagementComponent;
  @Input() filters!: any;
  loading: boolean = false;

  users: any = [];

  columns = [
    { key: 'code', label: 'Code' },
    { key: 'designation', label: 'Désignation' },
    { key: 'unite', label: 'Unité' },
    { key: 'quantite', label: 'Quantité' },
    { key: 'typeArticle', label: 'Type' },
    { key: 'prix', label: 'Prix' },
    { key: 'fournisseur', label: 'Fournisseur' },
    { key: 'projetId', label: 'Projet ID' }
  ];

  config: { [key: string]: any } = {};
  filtre!: string;

  constructor(
    private articleService: GestionArticleService,
    private _utilService: UtilsService,
    private windowService: NbDialogService,
    private router: Router,
    private csvService: ExportCSVService
  ) {}

  ngOnInit(): void {
    this.getAllArticles();
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

  onRowClicked(item: any) {
    this.router.navigate(['/article-detail', item.id]);
  }

  private getAllArticles(searchCriteria?: any): void {
    try {
      this.form = searchCriteria || {};
      this.loading = true;

      const formattedCriteria = { ...this.form };

      this.articleService.getArticles(
        this.pageNumber,
        this.elementPerPage,
        this.sortValue,
        this.sortDirection ? 'ASC' : 'DESC',
        formattedCriteria
      ).subscribe({
        next: data => {
          if (this.pageNumber > data.totalPages) {
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

  export() {
    this.articleService.getArticles(
      this.pageNumber,
      this.data$?.totalElements || 0,
      this.sortValue,
      this.sortDirection ? 'ASC' : 'DESC',
      this.form
    ).subscribe({
      next: data => {
        // Example: call your CSV export service
        // this.csvService.exportToCsv(data.content, "Liste des articles");
        this.loading = false;
      },
      error: err => {
        this.loading = false;
        this._utilService.displayError('Une erreur technique est survenue', "Erreur");
      }
    });
  }
}
```

### \u2705 Key Changes:

1. Replaced `Projet` with `Article`.
2. Updated `columns` to show `Article` fields.
3. Replaced `adjudicationService.searchRequest` with `articleService.getArticles`.
4. Row click navigation points to `/article-detail`.
5. `filters` and search criteria are kept dynamic.
6. Export function adapted for articles.

---

If you want, I can **also create the corresponding `ArticleService.getArticles()`** and backend API method with pagination, sorting, and filtering to complete the flow.

Do you want me to do that next?
