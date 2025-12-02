Got it! You want your **dynamic table** to support this new behavior:

### \u2705 **Goal**

For some columns (example: dynamically added `fournisseur` price columns),
\u27a1\ufe0f **do NOT generate search inputs**
\u27a1\ufe0f **do NOT include them in the search formGroup**
\u27a1\ufe0f **do NOT sort when clicking on them**

And this behavior must be dynamic and controlled by a boolean flag inside each column configuration.

---

# \u2705 **SOLUTION: Add a `searchable: boolean` and `sortable: boolean` to your column config**

### Example:

```ts
columns = [
  { key: 'code', label: 'Code', searchable: true, sortable: true },
  { key: 'designation', label: 'Désignation', searchable: true, sortable: true },
  { key: 'unite', label: 'Unité', searchable: true, sortable: true },
  { key: 'typeArticle', label: 'Type', searchable: true, sortable: true },

  // Dynamically generated fournisseur columns
  ...this.fournisseurHeaders.map(f => ({
    key: f,
    label: f,
    searchable: false,   // <-- DO NOT SEARCH
    sortable: false,     // <-- DO NOT SORT
    render: (row: any) => {
      const pa = row.priceArticles?.find(
        (p: any) => p.fournisseur?.raisonSociale === f
      );
      return pa ? pa.price : '-';
    }
  }))
];
```

---

# \u2705 **Step 1 \u2014 Modify Dynamic Table to ignore search for `searchable: false`**

### In **ngOnInit()**, change this:

```ts
this.columns.forEach(column => {
  this.searchFormGroup.addControl(column.key, this.fb.control(''));
});
```

### \u27a1\ufe0f Replace with:

```ts
this.columns.forEach(column => {
  if (column.searchable !== false) { 
    this.searchFormGroup.addControl(column.key, this.fb.control(''));
  }
});
```

---

# \u2705 **Step 2 \u2014 Hide search input in HTML when `searchable: false`**

Find this part:

```html
<td *ngFor="let column of columns">
  <form [formGroup]="searchFormGroup">
```

Wrap the input section:

```html
<td *ngFor="let column of columns">
  <form *ngIf="column.searchable !== false" [formGroup]="searchFormGroup">
      <!-- ALL YOUR SWITCH & INPUT CODE HERE -->
  </form>

  <!-- If not searchable, show empty cell OR static label -->
  <div *ngIf="column.searchable === false"></div>
</td>
```

---

# \u2705 **Step 3 \u2014 Disable sorting on non-sortable columns**

Currently:

```html
<td *ngFor="let column of columns" (click)="sortBy(column.key)">
```

Make it conditional:

```html
<td *ngFor="let column of columns" 
    (click)="column.sortable !== false ? sortBy(column.key) : null"
    [class.sort-disabled]="column.sortable === false">
```

Optional CSS:

```css
.sort-disabled {
  cursor: default;
  pointer-events: none;
  opacity: 0.6;
}
```

---

# \u2705 **Step 4 \u2014 Dynamic table: ignore sorting event if column is not sortable**

Inside `sortBy()`:

```ts
sortBy(key: any) {
  const column = this.columns.find(c => c.key === key);
  if (column?.sortable === false) return;  // <-- block sort

  this.keyword = key;
  this.sortValue.emit(this.keyword);
  this.direction = !this.direction;
  this.sortDirection.emit(this.direction);
}
```

---

# \U0001f389 **RESULT**

### Fournisseur columns:

\u2714 no search input
\u2714 no filter in the formGroup
\u2714 no sorting
\u2714 dynamic behavior

### Regular columns:

\u2714 search
\u2714 sort
\u2714 displayed normally

Your dynamic table now supports flexible configurations without breaking your generic logic.

---

If you want, I can also:
\u2705 clean the dynamic table code
\u2705 reorganize into smaller components
\u2705 add a "hide column" feature
Just tell me!
