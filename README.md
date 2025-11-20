You're getting:

```
NG01203: No value accessor for form control name: 'selectedFournisseur'
```

This error **always means one thing**:

\U0001f449 **Angular could not find a ControlValueAccessor for the component you're binding with `[(ngModel)]` or `formControlName`.**

In your case, the culprit is:

```html
<nb-radio
  name="selectedFournisseur"
  [value]="f"
  [(ngModel)]="selectedFournisseur"
  (valueChange)="onSelectFournisseur(f)">
</nb-radio>
```

`nb-radio` **cannot be used directly with `[(ngModel)]`**
because **Nebular\u2019s `<nb-radio>` is NOT a form control**.

Only `<nb-radio-group>` is compatible with Angular forms.

---

# \u2705 **FIX \u2014 Wrap radios inside `<nb-radio-group>`**

Replace your radio row with this:

```html
<nb-radio-group [(ngModel)]="selectedFournisseur" name="selectedFournisseur">
  <td *ngFor="let f of fournisseurs" class="p-3 text-center">
    <nb-radio [value]="f">
      {{ f }}
    </nb-radio>
  </td>
</nb-radio-group>
```

\u26a0\ufe0f Important
Move `[(ngModel)]` and `name` **to the group**, not individual radios.

---

# \U0001f4a1 If inside a table row, here is the correct structure

Angular does NOT require the `<nb-radio-group>` to be inside the `<tr>`.
So you can do this:

```html
<tr *ngIf="!data$.saved">
  <td class="fw-bold p-3">CHOISIR</td>

  <nb-radio-group [(ngModel)]="selectedFournisseur" name="selectedFournisseur">
    <td *ngFor="let f of fournisseurs" class="p-3 text-center">
      <nb-radio [value]="f"></nb-radio>
    </td>
  </nb-radio-group>

</tr>
```

---

# \U0001f9ea Component TS stays the same

```ts
selectedFournisseur?: string;

onSelectFournisseur(f: string) {
  this.selectedFournisseur = f;
}
```

(though `onSelectFournisseur` is no longer needed \u2014 Nebular handles it)

---

# \u2714\ufe0f After this change \u2192 Error disappears

No more:

```
No value accessor for form control name
```

---

If you want, I can help you add validation, highlighting selected supplier, or disabling the group when saved.
