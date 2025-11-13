Perfect \u2014 I understand the situation clearly now \u2705

Your code runs without any errors, **but** after import, the table only shows headers \u2014 the rows appear *only when you manually refresh the page*.

That means the problem is **not timing or errors**, but **Angular\u2019s change detection not re-rendering your dynamic table** after you update `this.columns` and `this.data$`.

Let\u2019s fix it cleanly \U0001f447

---

## \U0001f50d Why it happens

You are using a **dynamic columns array** and updating both:

```ts
this.columns = [...]
this.data$ = data
```

inside the same `subscribe`.
But your `<app-dynamic-table-back-management>` component likely uses `OnPush` change detection or `@Input()` bindings that only update when the reference *truly changes* **and** the parent view re-renders.

After the dialog closes, Angular doesn\u2019t trigger a change detection cycle automatically.

---

## \u2705 Solution 1 \u2014 Force refresh via `ChangeDetectorRef`

Inject `ChangeDetectorRef` and trigger an update after updating data and columns.

### \U0001f527 Modify your component:

```ts
import { ChangeDetectorRef } from '@angular/core';
```

Add it in your constructor:

```ts
constructor(
  private articleService: GestionArticleService,
  private _utilService: UtilsService,
  private windowService: NbDialogService,
  private router: Router,
  private csvService: ExportCSVService,
  private cdr: ChangeDetectorRef
) {}
```

Then inside your `getAllArticles()`\u2019s `next:` block, at the very end (after setting `this.data$` and `this.columns`):

```ts
this.data$ = data;
this.loading = false;
this.cdr.detectChanges(); // \U0001f448 Force refresh
```

This ensures the table fully re-renders with new data and columns.

---

## \u2705 Solution 2 \u2014 Recreate columns & data after close

Sometimes `DynamicTableBackManagementComponent` caches columns.
In that case, clear them before reload:

```ts
.onClose.subscribe((data) => {
  if (data === 'SUCCESS') {
    this.columns = []; // reset table columns
    this.data$ = undefined;
    setTimeout(() => this.getAllArticles(), 100); // small async delay
  }
})
```

---

## \u2705 Solution 3 \u2014 Verify `ArticleInjectionFormComponent` emits `SUCCESS`

In your import form component, make sure you close the dialog like this **only when the backend finishes saving:**

```ts
constructor(private dialogRef: NbDialogRef<ArticleInjectionFormComponent>) {}

onImportFinishedSuccessfully() {
  this.dialogRef.close('SUCCESS');
}
```

Otherwise the refresh won\u2019t trigger.

---

## \U0001f9e9 Recommended final setup

```ts
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
```

---

Would you like me to show you what your `ArticleInjectionFormComponent` should look like so that it correctly sends `'SUCCESS'` when import is done?
