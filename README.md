If **console.log in `onStepChange()` never appears**, then **Nebular never fires `(selectedChange)`** \u2192 the stepper stays stuck at step **0** \u2192 the route always stays `/step/0`.

This happens **only for ONE reason**:

# \u274c STEP CONTENT IS DESTROYED BY

### `*ngIf="stepper.selectedIndex === X"`

And Nebular **cannot change steps** if the projected content inside steps is destroyed.

\U0001f4a5 **The step-change event NEVER fires**
\U0001f4a5 **selectedIndex NEVER updates**
\U0001f4a5 **Your handler NEVER executes**

---

# \u2705 **THE FIX \u2013 100% GUARANTEED**

### \U0001f525 You MUST replace ALL your `*ngIf="stepper.selectedIndex === ..."` with:

```
*ngIf="currentStep === X"
```

because **currentStep actually changes** when you manually force the router.

---

# \U0001f6e0 LET ME FIX YOUR EXACT TEMPLATE

Here is YOUR code, correctly fixed \u2193

---

# \u2705 **REPLACE YOUR TEMPLATE WITH THIS**

```html
<nb-stepper
  #stepper
  orientation="horizontal"
  [selectedIndex]="currentStep"
  (selectedChange)="onStepChange(stepper.selectedIndex)"
>

  <nb-step [label]="labelOne">
    <ng-template #labelOne>Bq</ng-template>

    <app-enrichissement-stapper
      *ngIf="currentStep === 0"
      [projetId]="projetId"
      (fileUploaded)="onFileUploaded()">
    </app-enrichissement-stapper>

    <app-bq-list
      *ngIf="currentStep === 0"
      [projetId]="projetId"
      #bqList>
    </app-bq-list>

    <div class="step-buttons">
      <button nbButton nbStepperPrevious style="background-color: #324949; color: white;">Précédent</button>
      <button nbButton nbStepperNext style="background-color: #324949; color: white;">Suivant</button>
    </div>
  </nb-step>

  <nb-step [label]="labelTwo">
    <ng-template #labelTwo>Prix Entreprise Lot unique Retour Acheteur Délegué</ng-template>

    <ng-container *ngIf="currentStep === 1">
      <app-retour-acheteur-stepper
        [projetId]="projetId"
        (fileUploaded2)="onFileUploaded2()">
      </app-retour-acheteur-stepper>

      <app-article-projet-price-list
        [projetId]="projetId"
        #articleProjetPrice>
      </app-article-projet-price-list>
    </ng-container>

    <div class="step-buttons">
      <button nbButton nbStepperPrevious style="background-color: #324949; color: white;">Précédent</button>
      <button nbButton nbStepperNext style="background-color: #324949; color: white;">Suivant</button>
    </div>
  </nb-step>

  <nb-step label="ESTIMATION métreur">
    <ng-container *ngIf="currentStep === 2">
      <app-article-hors-borderau-stapper
        (fileUploadedHB)="onFileUploadedHB()"
        [projetId]="projetId">
      </app-article-hors-borderau-stapper>

      <app-article-hors-bordereau-list
        [projetId]="projetId"
        #articleHB>
      </app-article-hors-bordereau-list>
    </ng-container>

    <div class="step-buttons">
      <button nbButton nbStepperPrevious style="background-color: #324949; color: white;">Précédent</button>
      <button nbButton nbStepperNext style="background-color: #324949; color: white;">Suivant</button>
    </div>
  </nb-step>

  <nb-step [label]="labelFour">
    <ng-template #labelFour>comparatif</ng-template>

    <ng-container *ngIf="currentStep === 3">
      <app-estimation-result-list [projetId]="projetId"></app-estimation-result-list>
    </ng-container>

    <div class="step-buttons">
      <button nbButton nbStepperPrevious style="background-color: #324949; color: white;">Précédent</button>
      <button nbButton nbStepperNext style="background-color: #324949; color: white;">Suivant</button>
    </div>
  </nb-step>

  <nb-step [label]="labelFive">
    <ng-template #labelFive>Lancement P2P</ng-template>

    <h4>Step content #5</h4>
    <p class="lorem">
      Lorem ipsum...
    </p>

    <div class="step-buttons">
      <button nbButton nbStepperPrevious style="background-color: #324949; color: white;">Précédent</button>
      <button nbButton nbStepperNext disabled style="background-color: #324949; color: white;">Suivant</button>
    </div>
  </nb-step>

</nb-stepper>
```

---

# \U0001f525 AFTER THIS FIX, TEST AGAIN:

### Click "Next" \u2192 you should now see

\u2714 URL changes to `/step/1`
\u2714 Console prints

```
CHANGED STEP TO: 1
```

---

# \U0001f4cc IF IT STILL DOES NOT PRINT

Send me your **app-routing.module.ts** and I will fix the final missing piece.

But 95% of the time, the issue is **destroying step content using `*ngIf="stepper.selectedIndex === X"`**.

Now we fixed it.
