Thank you for the clarification! I understand your problem now: you want to **avoid infinite calls** to `getAllArticles()` while still ensuring that the article list **updates dynamically** when certain actions are taken in the `Enrichissement` component. Essentially, you want to **load articles once initially**, and only **trigger the call to fetch articles again after specific actions** (like completing the Enrichissement step) without causing an infinite loop.

### Solution Overview:

You need to make sure that `getAllArticles` is **only called when necessary**, and you should avoid triggering the call **repeatedly** unless the state changes (such as completing the Enrichissement step). Here's the correct approach:

### Steps:

1. **Initial Data Load**: When the step is first loaded, you fetch the data (articles).
2. **Handle Changes**: When the Enrichissement step completes, trigger a data reload (re-fetch the articles).
3. **Prevent Infinite Calls**: Make sure that `getAllArticles` is called only once per state change.

### Step-by-Step Solution:

1. **Use a Flag (`isEnrichissementCompleted`)** to track whether the Enrichissement step is completed.
2. **Avoid Multiple Calls** to `getAllArticles` unless necessary. You can do this by using the flag to determine when to call it again.

---

### **Step 1: Setup `StepperComponent`**

In the `StepperComponent`, you'll need to trigger the update of the article list **only when the Enrichissement step is done** (i.e., when the user uploads the file or completes the action). The key here is to use an `isEnrichissementCompleted` flag to prevent unnecessary calls.

```typescript
export class StepperComponent implements OnInit {
  projetId!: number;

  @ViewChild('articleList') articleList!: ArticleListComponent;
  @ViewChild('articleList2') articleList2!: ArticleListComponent;

  isEnrichissementCompleted: boolean = false; // Track if Enrichissement step is done

  constructor(private route: ActivatedRoute) {}

  ngOnInit(): void {
    this.projetId = Number(this.route.snapshot.paramMap.get('id'));
  }

  // Trigger article list refresh once Enrichissement is done
  onFileUploaded() {
    // Mark Enrichissement as completed
    this.isEnrichissementCompleted = true;

    // Trigger article list update based on the flag
    this.articleList.refreshArticles();
  }

  // When moving to the next step, reset flag if needed
  onStepChange() {
    if (this.isEnrichissementCompleted) {
      // If we come back to this step, ensure articles are loaded again
      this.articleList.refreshArticles();
    }
  }
}
```

### **Step 2: Update `ArticleListComponent` to Handle Refresh**

Now, update the `ArticleListComponent` to **fetch articles only once** unless explicitly triggered by a state change (e.g., completion of Enrichissement).

```typescript
export class ArticleListComponent implements OnInit {
  @Input() projetId!: number;
  @Input() showFournisseurPrix: boolean = false;
  @Input() isEnrichissementCompleted: boolean = false; // To track if the enrichissement is completed

  data$: ObjectPagination<Article> | undefined;
  loading: boolean = false;

  columns = [
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

  constructor(private articleService: GestionArticleService) {}

  ngOnInit(): void {
    this.loadArticlesIfNeeded();
  }

  ngOnChanges(): void {
    // Whenever input properties change, we check if we need to reload articles
    this.loadArticlesIfNeeded();
  }

  loadArticlesIfNeeded(): void {
    if (this.isEnrichissementCompleted && !this.data$) {
      this.getAllArticles();
    }
  }

  // Call this method to fetch the article data
  getAllArticles(): void {
    this.loading = true;
    const searchCriteria = { projetId: this.projetId };

    this.articleService.searchRequest(
      0, // page number
      10, // page size
      '', // sortValue
      'ASC', // sortDirection
      searchCriteria
    ).subscribe({
      next: (data) => {
        this.data$ = data;
        this.loading = false;
      },
      error: (err) => {
        this.loading = false;
        console.error('Error fetching articles:', err);
      }
    });
  }

  // Method to manually trigger article refresh (in case Enrichissement is completed after step)
  refreshArticles() {
    if (this.isEnrichissementCompleted) {
      this.getAllArticles();
    }
  }
}
```

### **Step 3: Pass Flag to `ArticleListComponent`**

In your HTML template, make sure to pass the `isEnrichissementCompleted` flag from the parent (`StepperComponent`) to the `ArticleListComponent` to control whether articles are fetched again.

```html
<nb-step [label]="labelOne">
  <ng-template #labelOne>Enrichissement</ng-template>

  <app-enrichissement-stapper [projetId]="projetId" (fileUploaded)="onFileUploaded()"></app-enrichissement-stapper>
  <app-article-list #articleList [projetId]="projetId" [showFournisseurPrix]="false" [isEnrichissementCompleted]="isEnrichissementCompleted"></app-article-list>

  <button nbButton disabled nbStepperNext>prev</button>
  <button nbButton nbStepperNext>next</button>
</nb-step>

<nb-step [label]="labelTwo">
  <ng-template #labelTwo>Retour Acheteur Délegué</ng-template>

  <app-retour-acheteur-stepper [projetId]="projetId" (fileUploaded2)="onFileUploaded2()"></app-retour-acheteur-stepper>
  <app-article-list #articleList2 [projetId]="projetId" [showFournisseurPrix]="true" [isEnrichissementCompleted]="isEnrichissementCompleted"></app-article-list>

  <button nbButton nbStepperPrevious>prev</button>
  <button nbButton nbStepperNext>next</button>
</nb-step>
```

---

### **Explanation:**

1. **Initial Load**: When `StepperComponent` is first loaded, it calls `getAllArticles()` to fetch the articles.
2. **Trigger after Enrichissement**: Once the user uploads the file or completes the `Enrichissement` step, the `onFileUploaded()` method is triggered, marking `isEnrichissementCompleted = true`. This causes the article list to be refreshed.
3. **Prevent Infinite Loops**: We ensure that `getAllArticles()` is only triggered when necessary \u2014 when `isEnrichissementCompleted` is `true` and the list has not already been loaded.
4. **Manual Refresh**: The `refreshArticles()` method can be used to manually trigger the article list refresh if needed (e.g., when the state changes).

---

### **Why This Solution Works:**

* **No Infinite API Calls**: The `getAllArticles()` method is only called **once** for the initial load and **again** only when the Enrichissement step is completed.
* **Efficient Updates**: The `ArticleListComponent` checks the flag (`isEnrichissementCompleted`) and ensures the data is only loaded if needed.
* **Avoid Re-renders**: Angular\u2019s change detection ensures that the view is updated only when required, without triggering unnecessary API calls.

This should solve the issue and ensure your app works efficiently without infinite calls! Let me know if this helps.
