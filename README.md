Ah, now it\u2019s clear \u2014 the problem is **not just `*ngIf`**. It\u2019s **how Nebular Stepper instantiates step content**.

Nebular **renders `<nb-step>` content lazily**: the component inside a step **does not exist in the DOM until the step is first activated**. That\u2019s why your `@ViewChild('bqList')` is undefined or exists too late. Even without `*ngIf`, Angular may not see the child immediately.

---

# \U0001f539 Reliable Solution: Use `NbStepperStepComponent.content` + `@ViewChild` OR a Service

There are **two clean ways**:

---

## **Option 1 \u2014 Trigger refresh from the child using a Subject (recommended)**

1. Create a **shared service**:

```ts
@Injectable({ providedIn: 'root' })
export class ArticleRefreshService {
  private refreshSubject = new Subject<void>();
  refresh$ = this.refreshSubject.asObservable();

  notifyRefresh() {
    this.refreshSubject.next();
  }
}
```

2. In your **Uploader Component (app-enrichissement-stapper)**:

```ts
constructor(private refreshSvc: ArticleRefreshService) {}

onUploadSuccess() {
  // call after backend save completes
  this.refreshSvc.notifyRefresh();
}
```

3. In your **List Component (app-bq-list)**:

```ts
constructor(private refreshSvc: ArticleRefreshService) {}

ngOnInit() {
  this.loadArticles();
  this.refreshSvc.refresh$.subscribe(() => {
    this.loadArticles();
  });
}
```

\u2705 This guarantees that the list will reload whenever the uploader emits, **even if the list is created later**.

---

## **Option 2 \u2014 Access the child via Step\u2019s `stepContent` after the step is selected**

1. Add a `@ViewChild(NbStepper)` in parent:

```ts
@ViewChild('stepper') stepper!: NbStepper;
```

2. Listen to `selectedIndexChange`:

```ts
onStepChange(index: number) {
  if (index === 0) {
    // now the step content exists
    if (this.bqListComponent) {
      this.bqListComponent.refreshList();
    }
  }
}
```

3. HTML:

```html
<nb-stepper #stepper (selectedIndexChange)="onStepChange($event)">
  <nb-step [label]="labelOne">
    <app-enrichissement-stapper
      [projetId]="projetId"
      (fileUploaded)="onFileUploaded()">
    </app-enrichissement-stapper>

    <app-bq-list [projetId]="projetId" #bqList></app-bq-list>
  </nb-step>
</nb-stepper>
```

\u2705 This ensures the step content exists **before calling `refreshList()`**.

---

# \U0001f539 My recommendation

Use **Option 1 with a Subject**. It solves **all lazy load / ViewChild timing issues** without hacks. It\u2019s also future-proof if you add more steps or more lists.

---

If you want, I can **rewrite your Stepper component fully** using the Subject approach so that:

* Uploader emits \u2192 list refreshes automatically
* Works with Nebular Stepper lazy loading
* No `*ngIf` needed
* Handles step switching perfectly

Do you want me to do that?
