Ahhh ok I understand your problem now \U0001f525
**Nebular Stepper loads ALL steps at initialization**, so every component in every step executes `ngOnInit()` immediately \u2014 even if the step is not selected.

That\u2019s why:

* Your `<app-enrichissement-stapper>` runs immediately
* Your `<app-bq-list>` also runs immediately
  **even before user selects Step 1**

You used this:

```html
<ng-container *ngIf="stepper.selectedIndex === 0">
```

to prevent all components from loading at once.

\u2714 This is correct, but it causes the problem that the second component only loads when step is reselected.

---

# \u2705 **Correct & clean solution**

Use *ngIf BUT also keep a reference to the component to reload data manually.

---

# \U0001f7e6 Step 1 \u2014 Keep your *ngIf, but wrap each component

```html
<nb-step [label]="labelOne">
  <ng-template #labelOne>Bq</ng-template>

  <!-- Component A -->
  <app-enrichissement-stapper
      *ngIf="stepper.selectedIndex === 0"
      [projetId]="projetId"
      (fileUploaded)="onFileUploaded()">
  </app-enrichissement-stapper>

  <!-- Component B -->
  <app-bq-list
      *ngIf="stepper.selectedIndex === 0"
      [projetId]="projetId"
      #bqList>
  </app-bq-list>

  <div class="step-buttons">
    <button nbButton nbStepperPrevious>Précédent</button>
    <button nbButton nbStepperNext>Suivant</button>
  </div>
</nb-step>
```

---

# \U0001f7e6 Step 2 \u2014 In parent TS: use ViewChild with `{ static: false }`

Because of *ngIf.

```ts
@ViewChild('bqList', { static: false }) bqListComponent!: BqListComponent;

onFileUploaded() {
  // Wait for Angular to create BqList again after *ngIf refresh
  setTimeout(() => {
    if (this.bqListComponent) {
      this.bqListComponent.refreshList();
    }
  });
}
```

The `setTimeout()` ensures the component exists before calling its method.

---

# \U0001f7e6 Step 3 \u2014 In Component B, add refresh method

```ts
refreshList() {
  this.loadArticles();
}
```

---

# \U0001f7e9 Result

\u2714 Components only load when step is selected (your requirement)
\u2714 Component B refreshes instantly after file upload
\u2714 No need to change steps
\u2714 No need to reload stepper
\u2714 No duplicate ngOnInit calls

---

# If you want, I can provide:

\u2728 A reusable solution using a **shared Subject** (RxJS)
\u2728 A solution using **global store** (NgRx / signals)
\u2728 A full refactor of your stepper architecture

Just tell me!
