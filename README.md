Below is **exactly the table you want**:

\u2714 Columns = **Fournisseurs (sorted by classement)**
\u2714 Rows = **ESTIMATION (CC)**, **ART HC**, **TOTAL**, **CLASSEMENT**
\u2714 Formatting similar to your example
\u2714 Easy to plug into Nebular `<nb-card>`
\u2714 Uses your returned structure:

```
{
  result: [
    { fournisseur, estimationCC, estimationHB, total, classement }
  ]
}
```

---

# \u2705 **1. Transform data in the component (TS)**

```ts
fournisseurs: string[] = [];
estimationCCRow: number[] = [];
estimationHBRow: number[] = [];
totalRow: number[] = [];
classementRow: number[] = [];

ngOnInit() {
  this.service.getEstimation(this.projetId).subscribe(res => {
    const result = res.result; // sorted by classement

    // 1. HEADER COLUMNS (fournisseur names)
    this.fournisseurs = result.map(r => r.fournisseur);

    // 2. ROWS
    this.estimationCCRow = result.map(r => r.estimationCC);
    this.estimationHBRow = result.map(r => r.estimationHB);
    this.totalRow = result.map(r => r.total);
    this.classementRow = result.map(r => r.classement);
  });
}
```

---

# \u2705 **2. HTML Table (NEBULAR) \u2014 EXACT FORMAT YOU WANT**

\U0001f449 **Copy/paste inside `<nb-card-body>`**

```html
<table class="table w-100 text-center mt-4" id="excel-table">

  <!-- ===== HEADER ===== -->
  <thead class="bg-header fw-bold">
  <tr class="border border-primary" style="background-color: whitesmoke;">
    <th class="p-3 border-end-white"> </th>

    <!-- Dynamic fournisseur columns -->
    <th *ngFor="let f of fournisseurs" class="p-3 border-end-white text-uppercase">
      {{ f }}
    </th>
  </tr>
  </thead>

  <tbody>

  <!-- ===== ESTIMATION (CC) ===== -->
  <tr>
    <td class="fw-bold p-3">ESTIMATION (CC)</td>
    <td *ngFor="let val of estimationCCRow" class="p-3">
      {{ val | number:'1.0-2':'fr' }}
    </td>
  </tr>

  <!-- ===== ART HC (estimation métreur) ===== -->
  <tr>
    <td class="fw-bold p-3">ART. HC (estimation métreur)</td>
    <td *ngFor="let val of estimationHBRow" class="p-3">
      {{ val | number:'1.0-2':'fr' }}
    </td>
  </tr>

  <!-- ===== TOTAL ===== -->
  <tr>
    <td class="fw-bold p-3">TOTAL</td>
    <td *ngFor="let val of totalRow" class="p-3">
      {{ val | number:'1.0-2':'fr' }}
    </td>
  </tr>

  <!-- ===== CLASSEMENT ===== -->
  <tr>
    <td class="fw-bold p-3">CLASSEMENT</td>
    <td *ngFor="let val of classementRow" class="p-3 fw-bold">
      {{ val }}
    </td>
  </tr>

  </tbody>
</table>
```

---

# \U0001f3a8 **3. Bonus: Styling for better visuals**

(You can add it to your component stylesheet)

```css
table th, table td {
  vertical-align: middle;
}

table .fw-bold {
  font-weight: 600;
}
```

---

# \U0001f3af **Result Example In Your UI**

|                | Baticam | Gromarbre | Loft 2 A | Decomarbre |
| -------------- | ------- | --------- | -------- | ---------- |
| **ESTIMATION** | 608 400 | 616 835   | 608 410  | 660 130    |
| **ART HC**     | 60 500  | 60 500    | 60 500   | 60 500     |
| **TOTAL**      | 670 880 | 697 135   | 698 610  | 740 430    |
| **CLASSEMENT** | 1       | 2         | 3        | 4          |

\u2714 **Your result maps perfectly**
\u2714 **Sorted by classement automatically**
\u2714 **Dynamic columns based on fournisseurs**

---

# If you want badges for classement (1st = gold, 2nd = silver, 3rd = bronze),

tell me: **"add classement badges"**.
