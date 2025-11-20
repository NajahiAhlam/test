Nebular **renders ALL `nb-step` contents at once** \u2014 meaning all your child components inside every step are created immediately.
That\u2019s why **all your methods (ngOnInit, API calls, file reading, etc.) execute on the first load**, even before the user navigates to that step.

\U0001f449 **Nebular stepper does NOT lazy-load steps.**
Angular still instantiates all components inside the template.

---

# \u2705 **Solution: Lazy load each step\u2019s content**

We must prevent Angular from creating the step content until the step becomes active.

The trick:
Use `*ngIf="stepper.selectedIndex === X"` for each step.

---

# \u2705 **How to implement lazy loading (step-by-step)**

### 1\ufe0f\u20e3 Add a template variable to your stepper

```html
<nb-stepper #stepper orientation="horizontal">
```

### 2\ufe0f\u20e3 Wrap each step content with an `ng-container` + `*ngIf`

Example for Step 1:

```html
<nb-step [label]="labelOne">
  <ng-template #labelOne>Bq</ng-template>

  <ng-container *ngIf="stepper.selectedIndex === 0">
    <app-enrichissement-stapper [projetId]="projetId" (fileUploaded)="onFileUploaded()"></app-enrichissement-stapper>
    <app-bq-list [projetId]="projetId"></app-bq-list>
  </ng-container>

  <div class="step-buttons">
    <button nbButton nbStepperPrevious>Précédent</button>
    <button nbButton nbStepperNext>Suivant</button>
  </div>
</nb-step>
```

### 3\ufe0f\u20e3 Step 2 example:

```html
<nb-step [label]="labelTwo">
  <ng-template #labelTwo>Prix Entreprise Lot unique Retour Acheteur Délegué</ng-template>

  <ng-container *ngIf="stepper.selectedIndex === 1">
    <app-retour-acheteur-stepper [projetId]="projetId" (fileUploaded2)="onFileUploaded2()"></app-retour-acheteur-stepper>
    <app-article-projet-price-list [projetId]="projetId"></app-article-projet-price-list>
  </ng-container>

  <div class="step-buttons">
    <button nbButton nbStepperPrevious>Précédent</button>
    <button nbButton nbStepperNext>Suivant</button>
  </div>
</nb-step>
```

### 4\ufe0f\u20e3 Repeat for all your steps.

---

# \U0001f4a1 TIP: Ensure it works even when going back

`selectedIndex` updates automatically with `nbStepperNext` and `nbStepperPrevious`.

So everything works seamlessly.

---

# \U0001f9e8 Result:

\u2714 Components inside steps load **only when their step becomes active**
\u2714 No more API calls executed at the beginning
\u2714 No more methods fired for steps the user didn't open yet
\u2714 Much faster initial page load

---

If you want, I can refactor your entire stepper code with all steps properly lazy-loaded.
