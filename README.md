You can make this **beautiful, clean, and easy for the user** without breaking your table layout by using a **"Choisir" selection row** with **Nebular radio buttons**, one button per fournisseur.

This keeps your comparatif table exactly as it is, AND gives the user a clear place to choose.

---

# \u2705 **\u2714 Best UI Choice: Add a \u201cCHOISIR FOURNISSEUR\u201d row with RADIO buttons**

### \u2714 Looks clean

### \u2714 Matches your table layout

### \u2714 Only one fournisseur can be selected

### \u2714 User action is obvious

### \u2714 Keeps the \u201ccomparatif financier\u201d aesthetic

---

# \U0001f31f **What it will look like (example)**

|             | Baticam | Gromarbre | Loft 2 A | Decomarbre |
| ----------- | ------- | --------- | -------- | ---------- |
| ESTIMATION  | 608 400 | 616 835   | 608 410  | 660 130    |
| ART HC      | 60 500  | 60 500    | 60 500   | 60 500     |
| TOTAL       | 670 880 | 697 135   | 698 610  | 740 430    |
| CLASSEMENT  | 1       | 2         | 3        | 4          |
| **CHOISIR** | \u25cf       | \u25cb         | \u25cb        | \u25cb          |

The user simply clicks a radio button \u2192 your component stores the chosen fournisseur.

---

# \U0001f9e9 **1. In your TS file**

```ts
selectedFournisseur: string | null = null;

onSelectFournisseur(f: string) {
  this.selectedFournisseur = f;
}
```

---

# \U0001f9e9 **2. Add a clean \u201cCHOISIR\u201d row to your HTML**

Insert this AFTER your \u201cCLASSEMENT\u201d row:

```html
<!-- ===== CHOISIR FOURNISSEUR ===== -->
<tr>
  <td class="fw-bold p-3">CHOISIR</td>

  <td *ngFor="let f of fournisseurs" class="p-3">
    <nb-radio
      name="selectedFournisseur"
      [value]="f"
      [(ngModel)]="selectedFournisseur"
      (valueChange)="onSelectFournisseur(f)">
    </nb-radio>
  </td>
</tr>
```

---

# \U0001f3af **3. Add a validation area / submit button**

Below the table:

```html
<div class="text-center mt-4">
  <button nbButton status="success" [disabled]="!selectedFournisseur">
    Valider le choix ({{ selectedFournisseur || '---' }})
  </button>
</div>
```

---

# \U0001fa84 **4. Add small styling to center radio buttons**

Optional:

```css
td nb-radio {
  margin: auto;
  display: block;
}
```

---

# \U0001f947 **Why this is the BEST solution**

* Maintains your table style
* Clear action for user
* Uses a single row \u2192 very clean
* The comparaison stays untouched
* Looks professional in Nebular UI
* Works responsively
* Avoids buttons in every column (which looks ugly)

---

# If you want a nicer version (gold/silver/bronze color for classement row) tell me:

\U0001f449 **\u201cAdd classement badges to the table\u201d**
