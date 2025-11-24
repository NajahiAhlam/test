If **`console.log("STEP CHANGED TO:", index);` doesn\u2019t show at all**, then **the event is NOT firing** \u2192 `nb-stepper` is **not emitting (selectedChange)** in your current structure.

This is a *very common Nebular issue* depending on the version and how the child components are loaded.

Let\u2019s troubleshoot step by step:

---

# \u2705 **1. Confirm your template**

Your stepper should look like this:

```html
<nb-stepper (selectedChange)="onStepChange($event)">
  <nb-step [label]="labelOne">
    <ng-template #labelOne>Step 1</ng-template>
    <app-step-one></app-step-one>
  </nb-step>

  <nb-step [label]="labelTwo">
    <ng-template #labelTwo>Step 2</ng-template>
    <app-step-two></app-step-two>
  </nb-step>
</nb-stepper>
```

### \u2757 If `(selectedChange)` is on a **child component** \u2192 it will NOT fire.

---

# \u2705 **2. Confirm your TS**

```ts
onStepChange(index: number) {
  console.log("STEP CHANGED TO:", index);
  this.currentStep = index;

  if (index === 0) {
    this.loadStepOne();
  } else if (index === 1) {
    this.loadStepTwo();
  }
}
```

---

# \u2757 If the event STILL does not fire

Then your Nebular version is one of the versions where **`selectedChange` does not work unless the stepper has a binding** (bug in several releases).

## Try adding a binding:

```html
<nb-stepper [(selectedIndex)]="currentStep" (selectedChange)="onStepChange($event)">
```

And in TS:

```ts
currentStep = 0;
```

If you **don\u2019t use [(selectedIndex)]**, the event often does not emit.

---

# \u2757 If STILL not working: Nebular lazy loading bug

You said earlier:

\u27a1\ufe0f **All methods of all steps execute at the beginning.**

This means your stepper is loading **all children immediately**, which only happens when:

\u2714 The parent component uses `*ngIf` around the stepper
\u2714 OR the stepper is inside a route with OnPush
\u2714 OR the stepper is wrapped in a structural directive from Nebular

This breaks `selectedChange`.

---

# \U0001f525 Fix for Nebular lazy-loading issue

Add this:

```ts
@ViewChild(NbStepperComponent) stepper!: NbStepperComponent;

ngAfterViewInit() {
  this.stepper.selectedChange.subscribe(index => {
    console.log("STEP CHANGED TO:", index);
    this.onStepChange(index);
  });
}
```

This bypasses the buggy template event.

---

# \u2714\ufe0f If this still doesn\u2019t fire

Then your `nb-stepper` is not rendering.

Do this test:

### Add this in HTML:

```html
<div style="border: 1px solid red">TEST</div>
```

If the TEST appears but `nb-stepper` content *does not display*, then you forgot:

```ts
import { NbStepperModule } from '@nebular/theme';
```

in your module.

---

# \U0001f9ea Final Diagnostic Checklist

Check each point:

| Check                                            | Explanation                         |
| ------------------------------------------------ | ----------------------------------- |
| `NbStepperModule` is imported                    | Otherwise stepper never initializes |
| `(selectedChange)` is on `<nb-stepper>`          | Not on `<nb-step>`                  |
| No `*ngIf` around the stepper                    | Breaks event emission               |
| Added `[(selectedIndex)]="currentStep"`          | Forces event binding                |
| Added manual subscription in `ngAfterViewInit()` | Fixes Nebular lazy-loading bugs     |

---

# \U0001f3af If you want, send me:

\U0001f449 **Your stepper HTML**
\U0001f449 **Your TS component**

I\u2019ll fix it exactly.
