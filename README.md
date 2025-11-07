Perfect \U0001f44d you already have most of the structure!
Let\u2019s wire everything step-by-step so your **stepper parent** passes the `projetId` to the **enrichissement child**, and the child uploads the Excel file to the backend.

---

## \U0001f9e9 1\ufe0f\u20e3 In Your Stepper Component (Parent)

Assuming your parent component is something like `StepperComponent`, update the TypeScript and template:

### **stepper.component.ts**

```ts
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute } from '@angular/router';

@Component({
  selector: 'app-stepper',
  templateUrl: './stepper.component.html',
  styleUrls: ['./stepper.component.scss']
})
export class StepperComponent implements OnInit {
  projetId!: number;

  constructor(private route: ActivatedRoute) {}

  ngOnInit(): void {
    this.projetId = Number(this.route.snapshot.paramMap.get('id')); // assuming route is /projet/:id
  }
}
```

---

### **stepper.component.html**

You simply **pass the id** as input to your child:

```html
<nb-step [label]="labelOne">
  <ng-template #labelOne>Enrichissement</ng-template>

  <app-enrichissement-stapper [projetId]="projetId"></app-enrichissement-stapper>

  <button nbButton disabled nbStepperNext>prev</button>
  <button nbButton nbStepperNext>next</button>
</nb-step>
```

---

## \U0001f9e9 2\ufe0f\u20e3 In the Child Component (`EnrichissementStapperComponent`)

Update it to receive the input and call your backend service for import.

### **enrichissement-stapper.component.ts**

```ts
import { Component, Input, OnDestroy, OnInit } from '@angular/core';
import { FormBuilder, FormGroup, Validators } from '@angular/forms';
import { Subscription } from 'rxjs';
import { UtilsService } from 'src/app/shared/services/utils.service';
import { EnrichissementService } from './enrichissement.service'; // create this service next

@Component({
  selector: 'app-enrichissement-stapper',
  templateUrl: './enrichissement-stapper.component.html',
  styleUrls: ['./enrichissement-stapper.component.scss']
})
export class EnrichissementStapperComponent implements OnInit, OnDestroy {

  @Input() projetId!: number;  // receive from parent

  processFormGroup!: FormGroup;
  submitted = false;
  formSubscription?: Subscription;

  constructor(
    private fb: FormBuilder,
    private utilsService: UtilsService,
    private enrichissementService: EnrichissementService
  ) {}

  ngOnInit(): void {
    this.initProcessFormGroup();
  }

  initProcessFormGroup() {
    this.processFormGroup = this.fb.group({
      file: [null, Validators.required]
    });
  }

  get form() {
    return this.processFormGroup.controls;
  }

  onFileSelected(event: any) {
    const file = event.target.files[0];
    if (file) {
      this.processFormGroup.patchValue({ file });
    }
  }

  onUploadFile() {
    this.submitted = true;

    if (!this.processFormGroup.valid) {
      return this.utilsService.displayError('Veuillez sélectionner un fichier Excel', 'Erreur');
    }

    const file = this.processFormGroup.get('file')!.value;
    this.enrichissementService.importArticles(file, this.projetId).subscribe({
      next: () => this.utilsService.displaySuccess('Fichier importé avec succès', 'Succès'),
      error: (err) => this.utilsService.displayError('Erreur lors de l\'import: ' + err.message, 'Erreur')
    });
  }

  ngOnDestroy(): void {
    this.formSubscription?.unsubscribe();
  }
}
```

---

### **enrichissement-stapper.component.html**

You can add the upload button here:

```html
<nb-card accent="primary" class="shadow-sm rounded-lg">
  <nb-card-header style="background-color: #324949;">
    <h5 class="title-heading text-uppercase my-auto p-2 mb-0" style="color: white;">
      Télécharger un modèle
    </h5>
  </nb-card-header>

  <nb-card-body>
    <div class="text-center py-4">
      <h5 style="color: #324949;" class="mb-3">Importer un fichier Excel</h5>

      <form [formGroup]="processFormGroup" novalidate class="w-75 mx-auto">
        <div class="border border-dashed border-2 rounded p-4">
          <label for="fileInput1" class="form-label text-muted mb-2 d-block">
            Choisissez un fichier à importer :
          </label>

          <input
            id="fileInput1"
            type="file"
            class="form-control"
            (change)="onFileSelected($event)"
          />

          <div
            class="invalid-feedback d-block text-left mt-2"
            *ngIf="submitted && form['file'].invalid"
          >
            <small>Veuillez sélectionner un fichier valide.</small>
          </div>
        </div>

        <div class="mt-3">
          <button nbButton status="primary" (click)="onUploadFile()">
            Importer le fichier
          </button>
        </div>
      </form>
    </div>
  </nb-card-body>
</nb-card>
```

---

## \U0001f9e9 3\ufe0f\u20e3 Service to Upload the File

### **enrichissement.service.ts**

```ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';

@Injectable({ providedIn: 'root' })
export class EnrichissementService {
  private baseUrl = '/api/articles'; // adjust if needed

  constructor(private http: HttpClient) {}

  importArticles(file: File, projetId: number): Observable<any> {
    const formData = new FormData();
    formData.append('file', file);

    return this.http.post(`${this.baseUrl}/import/${projetId}`, formData);
  }
}
```

---

\u2705 **Recap**

* The **stepper parent** gets the `projetId` from the route and passes it to the child.
* The **child** uses that `projetId` + selected Excel file to call the backend via the service.
* The **backend controller** already handles `@PostMapping("/import/{projetId}")`.

---

Would you like me to also show the backend controller route `/api/articles/import/{projetId}` to match this front-end call exactly?
