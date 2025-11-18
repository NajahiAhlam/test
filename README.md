Great \u2014 I understand exactly what you want.

\u2705 **Goal:**
In your **ProjetPriceArticle list** (not ArticleProjet), you want to dynamically generate **columns grouped by fournisseur**, exactly like your Article list:

```
| Article Code | Designation | ... | Atelier Hamma | Baticam | Exibat | ... |
```

But for **ProjetPriceArticle**, the structure is different:

Each row = *one fournisseur + one articleProjet + price*.
So instead of one row per article with several suppliers, you have **multiple rows per article** (one per supplier).

\U0001f449 To display suppliers in the header with prices below, you need to **pivot** the data exactly like you do for ArticleProjet.

I will give you:

---

# \u2705 1. Adjusted FRONTEND logic for ProjetPriceArticle

You need to:

### \u2714\ufe0f Collect all fournisseurs from the data

### \u2714\ufe0f Build a "pivoted" row for each articleProjet

### \u2714\ufe0f Render supplier column header and price under it

Here is the correct reusable logic.

---

# \u2705 2. Final Angular Code \u2014 Pivot ProjetPriceArticle

Replace your `getAllArticles()` but adapted for ProjetPriceArticle as `getAllProjetPriceArticles()`:

```ts
getAllProjetPriceArticles(searchCriteria?: any): void {
  try {
    this.form = searchCriteria || {};
    this.loading = true;

    this.projetPriceArticleService.searchRequest(
      this.pageNumber,
      this.elementPerPage,
      this.sortValue,
      this.sortDirection ? 'ASC' : 'DESC',
      { ...this.form, projetId: this.projetId }
    ).subscribe({

      next: (data) => {

        if (!data?.content?.length) {
          this.data$ = data;
          this.loading = false;
          return;
        }

        // === 1. Collect fournisseurs ===
        const fournisseurs = new Set<string>();
        data.content.forEach(row => {
          if (row.fournisseurName) {
            fournisseurs.add(row.fournisseurName);
          }
        });

        this.fournisseurHeaders = Array.from(fournisseurs);

        // === 2. Pivot rows ===
        const pivoted = this.pivotRows(data.content);

        // Replace content
        data.content = pivoted;

        // === 3. Build dynamic columns ===
        this.columns = [
          { key: 'articleCode', label: 'Code' },
          { key: 'articleDesignation', label: 'Désignation' },
          { key: 'unite', label: 'Unité' },
          { key: 'typeArticle', label: 'Type' },

          ...this.fournisseurHeaders.map(f => ({
            key: f,
            label: f,
            render: (row: any) => row[f] ?? '-'
          }))
        ];

        this.data$ = data;
        this.loading = false;
      },

      error: (err) => {
        console.error(err);
        this.loading = false;
      }
    });

  } catch (e) {
    console.error(e);
    this.loading = false;
  }
}
```

---

# \u2705 3. Pivoting Function (VERY IMPORTANT)

This groups all supplier prices under a single articleProjet row.

```ts
pivotRows(rows: any[]): any[] {
  const map = new Map<number, any>();

  rows.forEach(r => {
    if (!map.has(r.articleProjetId)) {
      map.set(r.articleProjetId, {
        articleProjetId: r.articleProjetId,
        articleCode: r.articleCode,
        articleDesignation: r.articleDesignation,
        unite: r.unite,
        typeArticle: r.typeArticle,
      });
    }

    const row = map.get(r.articleProjetId);
    row[r.fournisseurName] = r.price;
  });

  return Array.from(map.values());
}
```

---

# \U0001f525 Result Example

Input backend list:

```
[
  { articleProjetId: 1, articleCode: "50.01", fournisseurName: "Atelier Hamma", price: 30 },
  { articleProjetId: 1, articleCode: "50.01", fournisseurName: "Baticam", price: 40 },
  { articleProjetId: 2, articleCode: "50.02", fournisseurName: "Atelier Hamma", price: 8500 },
  ...
]
```

Pivoted result for UI:

```
[
  {
    articleProjetId: 1,
    articleCode: "50.01",
    articleDesignation: "...",
    Unite: "F",
    typeArticle: "...",
    "Atelier Hamma": 30,
    "Baticam": 40,
    ...
  },
  {
    articleProjetId: 2,
    articleCode: "50.02",
    "Atelier Hamma": 8500,
    "Baticam": 3000,
    ...
  }
]
```

---

# \U0001f9e9 Your dynamic table will now show:

### Header:

```
Code | Désignation | Unité | Type | Atelier Hamma | Baticam | Exibat | ...
```

### Row (for each articleProjet):

```
50.01 | Démolition ... | F | ... | 30 | 40 | 34 | ...
```

---

# \u2714\ufe0f Want me to generate:

\U0001f539 **Backend response DTO ready for pivoting?**
\U0001f539 **Controller method for ProjetPriceArticle?**
\U0001f539 **HTML table template?**
\U0001f539 **Angular service method?**

Just tell me!
