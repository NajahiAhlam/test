Here is **the correct Angular FRONT method** to call your backend `getAllArticleProjet` **WITHOUT changing your pagination model** `ObjectPagination`.

I give you:

### \u2705 Angular Service Method

### (Compatible with your backend signature: page, size, sortDirection, sortValue, params)

---

# \u2705 **Angular Service \u2013 getAllArticleProjet()**

```ts
getAllArticleProjet(
  page: number,
  size: number,
  sortDirection: string,
  sortValue: string,
  params: any
): Observable<ObjectPagination<ArticleProjet>> {

  let httpParams = new HttpParams()
    .set('page', page)
    .set('size', size)
    .set('sortDirection', sortDirection)
    .set('sortValue', sortValue);

  // dynamic filters (params Map)
  Object.keys(params).forEach(key => {
    if (params[key] !== null && params[key] !== '') {
      httpParams = httpParams.set(key, params[key]);
    }
  });

  return this.http.get<ObjectPagination<ArticleProjet>>(
    `${this.baseUrl}/article-projet`,
    { params: httpParams }
  );
}
```

---

# \U0001f4cc **Backend Expected URL Pattern**

Your backend expects:

```
GET /article-projet?page=0&size=10&sortDirection=ASC&sortValue=id&code=XX&designation=YY&projetId=1
```

The Angular method above produces exactly this.

---

# \U0001f4cc **Example Usage in Component**

```ts
loadArticles() {
  const filters = {
    code: this.searchCode,
    designation: this.searchDesignation,
    unite: this.searchUnite,
    typeArticle: this.searchType,
    quantite: this.searchQuantite,
    projetId: this.projectId
  };

  this.service.getAllArticleProjet(
    this.page,
    this.size,
    this.sortDirection,
    this.sortValue,
    filters
  ).subscribe(res => {
    this.articles = res.content;
    this.totalPages = res.totalPages;
  });
}
```

---

# \U0001f4cc **Your Pagination Model stays untouched**

Your model:

```ts
export class ObjectPagination<T> {
  content!: T[];
  totalElements!: number;
  totalPages!: number;
  size!: number;
  number!: number;
  first!: boolean;
  last!: boolean;
}
```

This works perfectly.

---

# If you want, I can generate:

\u2705 Controller in backend
\u2705 Full Angular table with dynamic filters
\u2705 Dialog for edit/update of ArticleProjet
\u2705 Sorting/Filtering UI

Just tell me.
