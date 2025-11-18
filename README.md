Perfect \u2014 now I **fully understand** what you need.

Your backend returns **one row per fournisseur**, like this:

```
ArticleProjetPriceDTO {
  id,
  price,
  articleCode,
  articleDesignation,
  fournisseurRaisonSociale,
  articleUnite
}
```

So an article appears **multiple times**, one for each fournisseur.

But in the **front-end**, you want this format:

### \u2705 Desired pivoted structure (group by articleCode)

| Code  | Désignation | Unité | Fournisseur A | Fournisseur B | Fournisseur C |
| ----- | ----------- | ----- | ------------- | ------------- | ------------- |
| 50.01 | Démolition\u2026 | F     | 33            | 20            | 32            |

---

# \u2705 **I will give you the correct** `getAllArticles()` **method for this backend response**

## \u2714 No tuple errors

## \u2714 Correct pivoting

## \u2714 Group rows by article

## \u2714 Generate fournisseur headers

## \u2714 Show prices under each fournisseur

## \u2714 Work with your dynamic table

---

# \u2b50 **Final Working Method \u2014 Paste It Directly**

```ts
getAllArticles(searchCriteria?: any): void {
  try {
    this.form = searchCriteria || {};
    this.loading = true;

    const formattedCriteria = { ...this.form, projetId: this.projetId };

    this.articleService.searchRequest(
      this.pageNumber,
      this.elementPerPage,
      this.sortValue,
      this.sortDirection ? 'ASC' : 'DESC',
      formattedCriteria
    ).subscribe({

      next: (data: any) => {

        const rows = data.content; // raw backend rows

        if (!rows || rows.length === 0) {
          this.data$ = data;
          this.loading = false;
          return;
        }

        // ---- 1) Build fournisseur headers ----
        const fournisseursSet = new Set<string>();
        rows.forEach((r: any) => {
          if (r.fournisseurRaisonSociale) {
            fournisseursSet.add(r.fournisseurRaisonSociale);
          }
        });
        this.fournisseurHeaders = Array.from(fournisseursSet);

        // ---- 2) Pivot rows by articleCode ----
        const map = new Map<string, any>();

        rows.forEach((r: any) => {
          const key = r.articleCode;

          if (!map.has(key)) {
            map.set(key, {
              code: r.articleCode,
              designation: r.articleDesignation,
              unite: r.articleUnite,
              // create fournisseur columns dynamically
              ...Object.fromEntries(
                this.fournisseurHeaders.map(f => [f, "-"])
              )
            });
          }

          const articleRow = map.get(key);
          articleRow[r.fournisseurRaisonSociale] = r.price;
        });

        const pivotedArray = Array.from(map.values());

        // ---- 3) Build columns dynamically ----
        this.columns = [
          { key: "code", label: "Code" },
          { key: "designation", label: "Désignation" },
          { key: "unite", label: "Unité" },
          ...this.fournisseurHeaders.map(f => ({
            key: f,
            label: f
          }))
        ];

        // ---- 4) Put pivoted into pagination object ----
        this.data$ = {
          ...data,
          content: pivotedArray
        };

        this.loading = false;
      },

      error: (err) => {
        console.error(err);
        this.loading = false;
        this._utilService.displayError("Une erreur technique est survenue", "Erreur");
      }

    });
  } catch (error) {
    console.error(error);
    this.loading = false;
    this._utilService.displayError("Une erreur inattendue est survenue", "Erreur");
  }
}
```

---

# \u2705 Output Example After Transformation

Backend gives:

```
50.01 + froclim + 33
50.01 + decomarbre + 20
50.01 + loft 2 a + 32
50.01 + exibat + 34
...
```

Front becomes:

```ts
[
  {
    code: "50.01",
    designation: "Démolition\u2026",
    unite: "F",
    froclim: 33,
    decomarbre: 20,
    "loft 2 a": 32,
    exibat: 34,
    ...
  }
]
```

---

# If you want, I can also generate:

\u2705 export to CSV with pivot
\u2705 sorting adaptation
\u2705 pagination adaptation
\u2705 filtering adaptation

Just say **"give me export code"** or **"give me csv pivot"**.
