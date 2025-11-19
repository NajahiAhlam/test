Below is the **cleanest and most professional UX** for your case:

\u2705 User clicks **Choisir** on any fournisseur
\u2705 If the fournisseur **is not classement = 1**, you show a **confirmation modal** + **comment textarea**
\u2705 If classement = 1 \u2192 accept immediately
\u2705 After confirmation, send the chosen fournisseur + comment to backend

I\u2019ll give you:

1\ufe0f\u20e3 How to add the \u201cChoisir\u201d button in your table
2\ufe0f\u20e3 The confirmation modal (Nebular dialog)
3\ufe0f\u20e3 Component TS logic (full, correct, copy-paste ready)

---

# \u2705 1. Add \u201cChoisir\u201d Button Under Each Fournisseur

Add a row **after CLASSEMENT**:

```html
<!-- ===== CHOIX ===== -->
<tr>
  <td class="fw-bold p-3">CHOIX</td>
  <td *ngFor="let f of fournisseurs; let i = index" class="p-3 text-center">
    <button nbButton status="primary" size="small"
            (click)="onChooseFournisseur(f, classementRow[i])">
      Choisir
    </button>
  </td>
</tr>
```

\u2714 You already have `classementRow` \u2192 we use the same index to know which fournisseur has which classement.

---

# \u2705 2. Create the Confirmation Dialog Component

Run:

```
ng g c choose-fournisseur-dialog
```

Then inside `choose-fournisseur-dialog.component.html`:

```html
<nb-card>
  <nb-card-header class="fw-bold">
    Confirmation du choix
  </nb-card-header>

  <nb-card-body>
    <p class="mb-2">
      Le fournisseur <b>{{ fournisseur }}</b> n\u2019a pas le classement <b>1</b>.
    </p>

    <p>Veuillez saisir un commentaire :</p>

    <textarea nbInput fullWidth rows="5"
              [(ngModel)]="comment"
              placeholder="Motif du choix...">
    </textarea>
  </nb-card-body>

  <nb-card-footer class="d-flex justify-content-end">
    <button nbButton status="danger" outline (click)="cancel()">
      Annuler
    </button>
    <button nbButton status="primary" class="ms-2" (click)="confirm()"
            [disabled]="!comment">
      Confirmer
    </button>
  </nb-card-footer>
</nb-card>
```

TS:

```ts
import { Component, Inject } from '@angular/core';
import { NbDialogRef, NB_DIALOG_CONFIG } from '@nebular/theme';

@Component({
  selector: 'app-choose-fournisseur-dialog',
  templateUrl: './choose-fournisseur-dialog.component.html'
})
export class ChooseFournisseurDialogComponent {
  fournisseur: string;
  comment: string = '';

  constructor(
    protected dialogRef: NbDialogRef<ChooseFournisseurDialogComponent>,
    @Inject(NB_DIALOG_CONFIG) config: any,
  ) {
    this.fournisseur = config.context.fournisseur;
  }

  confirm() {
    this.dialogRef.close(this.comment);
  }

  cancel() {
    this.dialogRef.close(null);
  }
}
```

---

# \u2705 3. Add Logic in Your Main Component

```ts
import { NbDialogService } from '@nebular/theme';
import { ChooseFournisseurDialogComponent } from './choose-fournisseur-dialog/choose-fournisseur-dialog.component';

export class ArticleProjetPriceListComponent {

  constructor(private dialogService: NbDialogService) {}

  onChooseFournisseur(fournisseur: string, classement: number) {
    if (classement === 1) {
      // Auto accept
      this.saveFournisseurChoice(fournisseur, null);
      return;
    }

    // Show confirmation dialog
    this.dialogService.open(ChooseFournisseurDialogComponent, {
      context: { fournisseur }
    })
    .onClose.subscribe(comment => {
      if (comment) {
        this.saveFournisseurChoice(fournisseur, comment);
      }
    });
  }

  saveFournisseurChoice(fournisseur: string, comment: string | null) {
    // TODO: backend call
    console.log('Fournisseur choisi:', fournisseur);
    console.log('Commentaire:', comment);

    // After success, you can:
    // this.toastr.success("Choix enregistré avec succès");
  }
}
```

---

# \U0001f389 Final Result (UX)

### If classement = **1**

\u2714 No popup
\u2714 User\u2019s choice is saved directly

### If classement \u2260 **1**

\u2714 Show confirmation dialog
\u2714 Ask for justification comment
\u2714 Save selection with comment

Beautiful, clean, professional, and business-friendly.

---

If you want, I can also:

\u2705 Generate the backend endpoint
\u2705 Store the selected fournisseur in database
\u2705 Add \u201cselected\u201d highlight in the table

Just tell me!
