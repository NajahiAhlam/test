Perfect \U0001f44d I understand what you want.
You want your **Angular table** in `ArticleListComponent` to dynamically show each **fournisseur as a column header**, and under each, display the **corresponding price** (like in Excel).

So your table headers will look like this:

| Code   | Désignation   | Unité | Type d\u2019article | Fournisseur A | Fournisseur B | Fournisseur C |
| ------ | ------------- | ----- | -------------- | ------------- | ------------- | ------------- |
| 200.14 | Revêtement... | ML    | Type 1         | 600           | 450           | 383           |

Let\u2019s do this **cleanly and dynamically** \U0001f447

---

### \u2705 Step 1: Add a variable to store dynamic fournisseur columns

In your component:

```ts
fournisseurHeaders: string[] = [];
```

---

### \u2705 Step 2: Detect fournisseurs dynamically from the first row of data

After fetching data in `getAllArticles()` (in your `next` block), do this:

```ts
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
```

---

### \u2705 Step 3: Remove the old \u201cFournisseurs et prix\u201d column

Remove this from your `columns` definition:

```ts
...(this.showFournisseurPrix ? [{
  key: 'priceArticles',
  label: 'Fournisseurs et prix',
  render: ...
}] : [])
```

Because now we are building **one column per fournisseur** dynamically.

---

### \u2705 Step 4: Make sure your dynamic table component supports dynamic columns

Your `DynamicTableBackManagementComponent` should use something like:

```html
<thead>
  <tr>
    <th *ngFor="let col of columns">{{ col.label }}</th>
  </tr>
</thead>
<tbody>
  <tr *ngFor="let row of data">
    <td *ngFor="let col of columns">
      {{ col.render ? col.render(row) : row[col.key] }}
    </td>
  </tr>
</tbody>
```

That\u2019s enough to make the dynamic fournisseur headers appear.

---

### \u2705 Step 5 (Optional): Sort or Export properly

When exporting to CSV, you can use:

```ts
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
```

---

### \u2705 Result

Now your table will look **exactly like your Excel**, for any number of fournisseurs:
\u2705 Each fournisseur appears as a separate column
\u2705 Each article shows the corresponding price under the right supplier
\u2705 Fully dynamic \u2014 updates automatically after import

---

Would you like me to modify your component code and show the full working `ArticleListComponent` (cleaned up version)?
