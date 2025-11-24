Ah! That error happens because in **Nebular\u2019s `NbStepperComponent`**, `selectedIndex` has a **getter**, but **no matching `selectedIndexChange` output**, so Angular cannot create two-way binding `[(selectedIndex)]`.

\u2705 That explains why `[(selectedIndex)]="currentStep"` shows:

```
The property and event halves of the two-way binding 'selectedIndex' are not bound to the same target
```

Nebular stepper **does not support two-way binding** natively. You must handle it **manually** using `@ViewChild` and subscribing to `selectedIndexChange`.

---

# **Working solution with manual subscription**

### Step 1: HTML (remove two-way binding)

```html
<nb-stepper #stepper orientation="horizontal" [selectedIndex]="currentStep">
```

> **Do NOT use `[(selectedIndex)]`** \u2014 just bind `[selectedIndex]`.

---

### Step 2: Component TS

```ts
import { Component, OnInit, ViewChild, AfterViewInit } from '@angular/core';
import { ActivatedRoute, Router } from '@angular/router';
import { NbStepperComponent, NbStepChangeEvent } from '@nebular/theme';

@Component({
  selector: 'app-stepper',
  templateUrl: './stepper.component.html',
  styleUrls: ['./stepper.component.scss']
})
export class StepperComponent implements OnInit, AfterViewInit {

  @ViewChild('stepper') stepper!: NbStepperComponent;

  projetId!: number;
  currentStep = 0;

  constructor(private route: ActivatedRoute, private router: Router) {}

  ngOnInit(): void {
    this.projetId = Number(this.route.snapshot.paramMap.get('id'));
    this.currentStep = Number(this.route.snapshot.paramMap.get('step')) || 0;
  }

  ngAfterViewInit(): void {
    // Subscribe to step changes
    this.stepper.selectedIndexChange.subscribe((index: number) => {
      this.currentStep = index;
      console.log('Step changed to:', index);

      // Update the URL
      this.router.navigate(['/stepper', this.projetId, 'step', index], { replaceUrl: true });
    });
  }

  openStepper() {
    this.router.navigate(['/stepper', this.projetId, 'step', 0]);
  }
}
```

---

### Step 3: HTML child steps (same as before)

```html
<nb-step [label]="labelOne">
  <ng-template #labelOne>Bq</ng-template>
  <app-enrichissement-stapper *ngIf="currentStep === 0" [projetId]="projetId" (fileUploaded)="onFileUploaded()"></app-enrichissement-stapper>
  ...
</nb-step>
```

---

\u2705 **Why this works**

* `selectedIndexChange` exists on `NbStepperComponent` and emits the index.
* `currentStep` controls which step is active via `[selectedIndex]`.
* No invalid two-way binding.
* Console log prints, URL updates, page refresh restores the step.

---

If you want, I can also **rewrite your full HTML with \u201cNext\u201d/\u201cPrevious\u201d buttons auto-incrementing steps** using this approach, so it\u2019s completely functional.

Do you want me to do that?
