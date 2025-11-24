You have a **Nebular Stepper** that resets to the **first step on page refresh**, because the stepper's `selectedIndex` is not being restored from anywhere.
Nebular has **no built-in route persistence**, so after refresh Angular rebuilds the component and stepper always starts at index `0`.

### \u2705 **Solution: Persist the selected step in the URL or in localStorage**

Below are the **two most common and clean ways**.

---

# \u2705 **OPTION 1 \u2014 Persist step in URL (recommended)**

Each step corresponds to a URL:

* `/projet/123/step/0`
* `/projet/123/step/1`
* ...

### \u2714 Step 1: Update template

Bind selectedIndex to a variable:

```html
<nb-stepper
  #stepper
  [selectedIndex]="currentStep"
  (selectedChange)="onStepChange($event)"
>
```

### \u2714 Step 2: In your component:

```ts
currentStep = 0;

constructor(private route: ActivatedRoute, private router: Router) {}

ngOnInit() {
  this.currentStep = Number(this.route.snapshot.paramMap.get('step')) || 0;
}

onStepChange(index: number) {
  this.currentStep = index;

  this.router.navigate(
    ['/projet', this.projetId, 'step', index],
    { replaceUrl: true }
  );
}
```

### \U0001f4a5 Result

If you're on step **3** and you refresh \u2192 URL still contains `/step/3` \u2192 stepper restores **step 3**.

---

# \u2705 **OPTION 2 \u2014 Save step index in localStorage**

Simplest if you don\u2019t want URL changes.

### \u2714 Template

```html
<nb-stepper
  #stepper
  [selectedIndex]="currentStep"
  (selectedChange)="onStepChange($event)"
>
```

### \u2714 Component

```ts
currentStep = 0;

ngOnInit() {
  const savedStep = localStorage.getItem('stepperIndex');
  if (savedStep !== null) {
    this.currentStep = Number(savedStep);
  }
}

onStepChange(index: number) {
  localStorage.setItem('stepperIndex', index.toString());
}
```

### \U0001f4a5 Result

Refresh \u2192 stepper resumes where you left off.

---

# \u26a0 Why your current code always resets to step 0

Because `*ngIf="stepper.selectedIndex === X"` is evaluated **after rendering**, and the stepper always initializes to index **0**.

Nebular stepper does NOT store its internal state across refresh.

---

# \U0001f91d Want me to integrate the fix directly into your full component code?

If yes, tell me which option you prefer:

### \U0001f539 Option 1 \u2014 URL-based persistence (best for sharing links & multi-users)

### \U0001f539 Option 2 \u2014 LocalStorage persistence (simplest)

I\u2019ll rewrite your component cleanly.
