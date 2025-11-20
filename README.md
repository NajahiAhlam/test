Perfect! Let\u2019s rewrite your **Nebular Stepper** fully, implementing the behavior you want:

* **Step 1:** User uploads list in `app-enrichissement-stapper`.
* **Step 1:** `app-bq-list` automatically reloads and shows the new data.
* **Step 2+**: Other steps work normally.
* **No race conditions**, no `*ngIf` hacks or `setTimeout`.

We\u2019ll use a **shared service with a Subject** to communicate between uploader and list.

---

## **1\ufe0f\u20e3 ArticleRefreshService**

```ts
// article-refresh.service.ts
import { Injectable } from '@angular/core';
import { Subject } from 'rxjs';

@Injectable({ providedIn: 'root' })
export class ArticleRefreshService {
  private refreshSubject = new Subject<void>();
  refresh$ = this.refreshSubject.asObservable();

  notifyRefresh() {
    this.refreshSubject.next();
  }
}
```

---

## **2\ufe0f\u20e3 Stepper Component TS**

```ts
import { Component, ViewChild } from '@angular/core';
import { NbStepperComponent } from '@nebular/theme';
import { BqListComponent } from './bq-list/bq-list.component';
import { ArticleRefreshService } from './article-refresh.service';

@Component({
  selector: 'app-stepper',
  templateUrl: './stepper.component.html'
})
export class StepperComponent {
  @ViewChild('stepper') stepper!: NbStepperComponent;

  projetId = 1; // example projetId

  constructor(private refreshSvc: ArticleRefreshService) {}

  onFileUploaded() {
    // Notify the list to refresh via service
    this.refreshSvc.notifyRefresh();
  }
}
```

---

## **3\ufe0f\u20e3 Stepper HTML**

```html
<nb-stepper #stepper linear>
  <!-- STEP 1 -->
  <nb-step label="Bq">
    <app-enrichissement-stapper
      [projetId]="projetId"
      (fileUploaded)="onFileUploaded()">
    </app-enrichissement-stapper>

    <app-bq-list [projetId]="projetId"></app-bq-list>

    <div class="step-buttons">
      <button nbButton nbStepperPrevious>Précédent</button>
      <button nbButton nbStepperNext>Suivant</button>
    </div>
  </nb-step>

  <!-- STEP 2 -->
  <nb-step label="Step 2">
    <p>Step 2 content</p>
    <div class="step-buttons">
      <button nbButton nbStepperPrevious>Précédent</button>
      <button nbButton nbStepperNext>Suivant</button>
    </div>
  </nb-step>

  <!-- STEP 3 -->
  <nb-step label="Step 3">
    <p>Step 3 content</p>
    <div class="step-buttons">
      <button nbButton nbStepperPrevious>Précédent</button>
      <button nbButton nbStepperNext>Suivant</button>
    </div>
  </nb-step>
</nb-stepper>
```

---

## **4\ufe0f\u20e3 BqList Component**

```ts
import { Component, Input, OnInit, OnDestroy } from '@angular/core';
import { Subscription } from 'rxjs';
import { ArticleRefreshService } from '../article-refresh.service';
import { ProjetArticleService } from '../services/projet-article.service';

@Component({
  selector: 'app-bq-list',
  template: `
    <p>Articles:</p>
    <ul>
      <li *ngFor="let a of articles">{{ a.designation }}</li>
    </ul>
  `
})
export class BqListComponent implements OnInit, OnDestroy {
  @Input() projetId!: number;
  articles: any[] = [];
  private sub: Subscription = new Subscription();

  constructor(
    private refreshSvc: ArticleRefreshService,
    private projetArticleService: ProjetArticleService
  ) {}

  ngOnInit() {
    this.loadArticles(); // initial load

    // Subscribe to refresh event from uploader
    this.sub.add(
      this.refreshSvc.refresh$.subscribe(() => {
        console.log("\U0001f525 BqList refreshing after file upload");
        this.loadArticles();
      })
    );
  }

  loadArticles() {
    this.projetArticleService.getArticles(this.projetId).subscribe((data) => {
      this.articles = data;
    });
  }

  ngOnDestroy() {
    this.sub.unsubscribe();
  }
}
```

---

## **5\ufe0f\u20e3 app-enrichissement-stapper Component**

```ts
import { Component, Input, Output, EventEmitter } from '@angular/core';

@Component({
  selector: 'app-enrichissement-stapper',
  template: `
    <button nbButton (click)="uploadFile()">Upload File</button>
  `
})
export class EnrichissementStapperComponent {
  @Input() projetId!: number;
  @Output() fileUploaded = new EventEmitter<void>();

  uploadFile() {
    // Simulate backend save
    setTimeout(() => {
      console.log("\u2705 File uploaded successfully");
      this.fileUploaded.emit(); // notify parent
    }, 1000);
  }
}
```

---

## \u2705 How it works

1. Step 1 is active \u2192 both components rendered.
2. User clicks **Upload File** \u2192 uploader component emits `(fileUploaded)`.
3. Stepper parent receives event \u2192 calls `ArticleRefreshService.notifyRefresh()`.
4. `BqListComponent` subscribes to `refresh$` \u2192 reloads articles.
5. Step 2 and Step 3 are lazy-loaded and unaffected.

This **avoids all ViewChild race conditions**, works inside the same step, and keeps your per-step lazy rendering intact.

---

I can also make a **fully working StackBlitz example** with real upload + list if you want \u2014 so you can just copy/paste it into your project.

Do you want me to do that?
