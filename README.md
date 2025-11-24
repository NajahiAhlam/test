Ah! Now it\u2019s fully clear. In the **current Nebular version**, `NbStepperComponent`:

* **Does NOT have `selectedIndexChange`**
* **Does NOT have `selectedChange`**
* Only exposes **`selectedIndex`** (read/write property)

This is why **all two-way binding attempts fail**.

\u2705 That means **the only reliable way to track step changes** is **via `(nbStepperNext)` and `(nbStepperPrevious)` events** on the buttons, because Nebular emits **events when you click \u201cNext\u201d or \u201cPrevious\u201d**, not automatically when the index changes.

---

# **Working Approach Using Buttons**

### Step 1: HTML

```html
<nb-stepper #stepper orientation="horizontal" [selectedIndex]="currentStep">

  <!-- STEP 1 -->
  <nb-step [label]="labelOne">
    <ng-template #labelOne>Bq</ng-template>

    <app-enrichissement-stapper *ngIf="currentStep === 0" [projetId]="projetId" (fileUploaded)="onFileUploaded()"></app-enrichissement-stapper>

    <div class="step-buttons">
      <button nbButton (click)="previousStep()" [disabled]="currentStep === 0">Précédent</button>
      <button nbButton (click)="nextStep()" [disabled]="currentStep === 4">Suivant</button>
    </div>
  </nb-step>

  <!-- STEP 2 -->
  <nb-step [label]="labelTwo">
    <ng-container *ngIf="currentStep === 1">
      <app-retour-acheteur-stepper [projetId]="projetId" (fileUploaded2)="onFileUploaded2()"></app-retour-acheteur-stepper>
    </ng-container>

    <div class="step-buttons">
      <button nbButton (click)="previousStep()">Précédent</button>
      <button nbButton (click)="nextStep()">Suivant</button>
    </div>
  </nb-step>

  <!-- Add other steps similarly -->
</nb-stepper>
```

---

### Step 2: Component TS

```ts
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute, Router } from '@angular/router';

@Component({
  selector: 'app-stepper',
  templateUrl: './stepper.component.html',
  styleUrls: ['./stepper.component.scss']
})
export class StepperComponent implements OnInit {

  projetId!: number;
  currentStep = 0;
  maxStep = 4; // total steps - 1

  constructor(private route: ActivatedRoute, private router: Router) {}

  ngOnInit(): void {
    this.projetId = Number(this.route.snapshot.paramMap.get('id'));
    this.currentStep = Number(this.route.snapshot.paramMap.get('step')) || 0;
  }

  nextStep() {
    if (this.currentStep < this.maxStep) {
      this.currentStep++;
      this.updateRoute();
    }
  }

  previousStep() {
    if (this.currentStep > 0) {
      this.currentStep--;
      this.updateRoute();
    }
  }

  private updateRoute() {
    console.log('Step changed to:', this.currentStep);
    this.router.navigate(['/stepper', this.projetId, 'step', this.currentStep], { replaceUrl: true });
  }
}
```

---

### \u2705 Why this works

1. Nebular does **not emit step-change automatically** in latest versions.
2. The step index is controlled manually via `currentStep`.
3. \u201cNext\u201d and \u201cPrevious\u201d buttons update `currentStep` and the route.
4. Child components load only for the active step (`*ngIf="currentStep === X"`).
5. Page refresh restores the step from the URL (`/stepper/:id/step/:step`).

---

If you want, I can now rewrite your **full 5-step stepper HTML and TS** with this button-based solution so it\u2019s fully functional.

Do you want me to do that?
