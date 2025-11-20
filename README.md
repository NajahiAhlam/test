Ah, I see the issue. When you use the `<nb-radio-group>`, you need to make sure that it is properly set up to display each radio button and also bind the `[(ngModel)]` correctly.

Let\u2019s address the issue:

### Possible cause of the issue:

1. The `<nb-radio-group>` should wrap all the `<nb-radio>` elements.
2. If it's not showing, it could be due to how the layout of `<td>` and `<nb-radio>` is set up inside the table.

Let's fix the issue step by step.

---

### \u2705 **Correct Structure with `nb-radio-group`**

Here is how the **correct structure** should look for your case:

```html
<nb-card accent="primary">
  <ng-container *ngIf="data$ && hasNonZeroHB">
    <ng-container *ngIf="loading">
      <rpa-portal-loading></rpa-portal-loading>
    </ng-container>

    <nb-card-body>
      <table class="table w-100" id="excel-table" aria-label="This table displays the data related to filters">
        <thead class="bg-header fw-bold">
          <tr class="border" style="background-color: whitesmoke;">
            <th class="p-3 border-end-white"></th>
            <th *ngFor="let f of fournisseurs" class="p-3 border-end-white text-uppercase">{{ f }}</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td class="fw-bold p-3">ESTIMATION</td>
            <td *ngFor="let val of estimationCCRow" class="p-3">
              {{ val | number: '1.0-2': 'fr' }}
            </td>
          </tr>
          <tr>
            <td class="fw-bold p-3">ART. HC (estimation métreur)</td>
            <td *ngFor="let val of estimationHBRow" class="p-3">
              {{ val | number: '1.0-2': 'fr' }}
            </td>
          </tr>
          <tr>
            <td class="fw-bold p-3">TOTAL</td>
            <td *ngFor="let val of totalRow" class="p-3">
              {{ val | number: '1.0-2': 'fr' }}
            </td>
          </tr>
          <tr>
            <td class="fw-bold p-3">CLASSEMENT</td>
            <td *ngFor="let val of classementRow" class="p-3 fw-bold">
              {{ val }}
            </td>
          </tr>

          <!-- Row for radio buttons -->
          <tr *ngIf="!data$.saved">
            <td class="fw-bold p-3">CHOISIR</td>
            <td *ngFor="let f of fournisseurs" class="p-3 text-center">
              <nb-radio-group [(ngModel)]="selectedFournisseur" name="selectedFournisseur">
                <nb-radio [value]="f">
                  {{ f }}
                </nb-radio>
              </nb-radio-group>
            </td>
          </tr>
        </tbody>
      </table>
    </nb-card-body>
  </ng-container>
</nb-card>
```

---

### \u2705 **Key Fixes:**

1. **Wrap `nb-radio` with `nb-radio-group`:**
   The `[(ngModel)]` and `name` must be applied **on the `nb-radio-group`** itself, not on the individual radio buttons.

2. **Use `nb-radio-group` inside a single `td`:**
   If your radio buttons are inside a table, the group must wrap them, and `*ngFor` must loop over the `fournisseurs` within `nb-radio`.

---

### \u2753 **How It Works:**

* `nb-radio-group` binds the value of the selected radio button with `[(ngModel)]="selectedFournisseur"`.
* Each `nb-radio` has a `[value]="f"`, which ensures that the value of the selected radio button is assigned to `selectedFournisseur` when the user clicks it.

---

### **Check the Following:**

1. Ensure `fournisseurs` is an array of **strings** (or values you're assigning to the radio buttons) in the TypeScript component.
2. **Double-check the `selectedFournisseur`** in your component: It must be a string (the selected `f` value).

---

### \u2705 **Example TypeScript:**

```ts
export class EstimationResultListComponent {
  fournisseurs: string[] = ['Fournisseur 1', 'Fournisseur 2', 'Fournisseur 3'];
  selectedFournisseur?: string;

  onSelectFournisseur(f: string) {
    this.selectedFournisseur = f;
  }

  // Other code...
}
```

---

This should now show the radio buttons correctly.
Let me know if it works or if you need further adjustments!
