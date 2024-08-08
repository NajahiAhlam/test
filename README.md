node_modules\pace-js\pace.min.js:6 ERROR 
Error: NG01352: If ngModel is used within a form tag, either the name attribute must be set or the form
    control must be defined as 'standalone' in ngModelOptions.

    Example 1: <input [(ngModel)]="person.firstName" name="first">
    Example 2: <input [(ngModel)]="person.firstName" [ngModelOptions]="{standalone: true}">
    at missingNameException (forms.mjs:3736:12)
    at NgModel._checkName (forms.mjs:4058:19)
    at NgModel._checkForErrors (forms.mjs:4041:14)
    at NgModel.ngOnChanges (forms.mjs:3968:14)
    at NgModel.rememberChangeHistoryAndInvokeOnChangesHook (core.mjs:3032:14)
    at callHookInternal (core.mjs:4024:14)
    at callHook (core.mjs:4055:9)
    at callHooks (core.mjs:4006:17)
    at executeInitAndCheckHooks (core.mjs:3956:9)
    at selectIndexInternal (core.mjs:11780:17)
3
request-auth-interceptor.service.ts:17 Token was successfully refreshed
deposer-fichier.component.ts:52 selectedOption 
deposer-fichier.component.ts:66 Unknown process selected
uploadFile	@	deposer-fichier.component.ts:66
onFileSelected	@	deposer-fichier.component.ts:39
DeposerFichierComponent_Template_input_change_15_listener	@	deposer-fichier.component.html:19
Zone - HTMLInputElement.addEventListener:change (asynchrone)		
DeposerFichierComponent_Template	@	deposer-fichier.component.html:19
load (asynchrone)		
p	@	node_modules\pace-js\pace.min.js:6
a	@	node_modules\pace-js\pace.min.js:6
nt.watch	@	node_modules\pace-js\pace.min.js:6
(anonyme)	@	node_modules\pace-js\pace.min.js:6
tt.trigger	@	node_modules\pace-js\pace.min.js:6
r.open	@	node_modules\pace-js\pace.min.js:6
Zone - Promise.then (asynchrone)		
getUserEmail	@	auth-interceptor.service.ts:41
handleRequest	@	auth-interceptor.service.ts:20
intercept	@	auth-interceptor.service.ts:13
intercept	@	http-error-handling-…eptor.service.ts:15
(anonyme)	@	request-auth-interceptor.service.ts:21
intercept	@	request-auth-interceptor.service.ts:14
Zone - Promise.then (asynchrone)		
loadChildren	@	app-routing.module.ts:9
Afficher 1 449 autres frames
<nb-card accent="primary" class="">
    <nb-card-header>
      <h5 class="title-animation title-heading text-uppercase my-auto p-2">Traitement Cockpit - 
        <span style="color:#DC3545">Déposer un fichier</span>
      </h5>
    </nb-card-header>
    <nb-card-body>
      <div class="d-flex justify-content-center box">
        <div class="w-50">
          <form>
            <nb-select fullWidth placeholder="Choisir un processus" [(ngModel)]="selectedOption" (selectionChange)="onProcessChange($event)">
              <nb-option *ngFor="let option of options" [value]="option">{{ option }}</nb-option>
            </nb-select>
          </form>
        </div>
        <div class="upload-container">
          <div class="upload-box" id="drop-area">
            <label for="fileElem" class="m-auto w-100">
              <input type="file" id="fileElem" (change)="onFileSelected($event)" accept=".csv, application/vnd.openxmlformats-officedocument.spreadsheetml.sheet, application/vnd.ms-excel">
              <div class="upload-box-img">
                <nb-icon icon="file-excel-2-line"></nb-icon>
              </div>
              <div class="upload-box-description">
                <p>Faites glisser et déposez un fichier ici ou <span class="fw-bold text-danger">choisissez un fichier</span><br>
                  <span class="text-muted">Xlsx, xls (10 MB Max) par fichier</span>
                </p>
              </div>
            </label>
          </div>
          <br>
          <div *ngIf="isUploading" class="upload-progress d-flex justify-content-between shadow d-flex flex-row">
            <div class="col-lg-1 upload-progress-icon my-auto">
              <nb-icon icon="file-text-line"></nb-icon>
            </div>
            <div class="col-md-8 col-lg-10 upload-progress-values my-auto">
              <div class="file-name fw-bold">{{ selectedFile?.name }}</div>
              <div class="file-size text-muted">{{ fileSize }}</div>
              <div class="progress-bar w-100">
                <div [ngStyle]="{ width: (progress) + '%' }" class="progress-bar-value"></div>
              </div>
            </div>
            <div class="col-lg-1 cancel-icon my-auto" (click)="cancelUpload()">
              <nb-icon icon="xmark" pack="fas"></nb-icon>
            </div>
          </div>
        </div>
      </div>
    </nb-card-body>
  </nb-card>
  import { Component } from '@angular/core';
import { FormBuilder, FormGroup } from '@angular/forms';
import { NbWindowService } from '@nebular/theme';
import { catchError, Observable, Subscription, throwError } from 'rxjs';
import { UtilsService } from 'src/app/@core/services/utils.service';
import { ExcelService } from '../../service/excel.service';

@Component({
  selector: 'app-deposer-fichier',
  templateUrl: './deposer-fichier.component.html',
  styleUrls: ['./deposer-fichier.component.scss']
})
export class DeposerFichierComponent {
  options = ['Extract du Suivi des Portefeuilles Projets', 
             'Sciforma Suivi des Timesheets SGMA', 
             'P2P du Suivi des Engagements'];
  selectedOption: string = '';
  isUploading: boolean = false;
  selectedFile!: File;
  progress = 0;
  fileSize = '';
  
  private fileSendSubscription?: Subscription;

  constructor(
    private fb: FormBuilder,
    private utilsService: UtilsService,
    private windowService: NbWindowService,
    private excelService: ExcelService) {
  }

  onFileSelected(event: Event): void {
    const target = event.target as HTMLInputElement;
    const files = target.files;

    if (files && files.length > 0) {
      this.selectedFile = files[0];
      this.fileSize = (this.selectedFile.size / (1024 * 1024)).toFixed(2) + ' MB';
      this.uploadFile(this.selectedFile);
    }
  }

  onProcessChange(event: any) {
    this.selectedOption = event.value;
  }

  uploadFile(file: File): void {
    const formData = new FormData();
    formData.append('file', file, file.name);
  
    let uploadObservable: Observable<any> | undefined;
    console.log("selectedOption", this.selectedOption)
    switch (this.selectedOption) {
      case 'Extract du Suivi des Portefeuilles Projets':
        uploadObservable = this.excelService.uploadFilee(formData);
        break;
      case 'Sciforma Suivi des Timesheets SGMA':
        // Assign the appropriate service method for this case
        // uploadObservable = this.sciformaService.uploadFile(formData);
        break;
      case 'P2P du Suivi des Engagements':
        // Assign the appropriate service method for this case
        // uploadObservable = this.p2pService.uploadFile(formData);
        break;
      default:
        console.error('Unknown process selected');
        return; // Exit if no valid option is selected
    }
  
    if (uploadObservable) {
      uploadObservable.pipe(
        catchError(error => {
          console.error('Error uploading file:', error);
          return throwError(error);
        })
      ).subscribe({
        next: (data) => {
          this.utilsService.displaySucess("Piece jointe ajoutée avec succès", "WorkFlow Cockpit");
          this.resetUpload();
        },
        error: (err) => {
          console.error('Upload error:', err);
        }
      });
    } else {
      console.error('No upload observable available');
    }
  }
  
  handleUploadProgress(event: any) {
    if (event.lengthComputable) {
      this.progress = Math.round((100 * event.loaded) / event.total);
    }
  }

  handleError(error: any) {
    console.error('Upload error:', error);
    this.isUploading = false;
  }

  handleComplete() {
    console.log('Upload complete');
    this.isUploading = false;
    this.resetUpload();
  }

  cancelUpload() {
    this.isUploading = false;
  }

  resetUpload() {
    this.selectedFile = null as any;
    this.progress = 0;
    this.fileSize = '';
  }

  ngOnDestroy(): void {
    this.fileSendSubscription?.unsubscribe();
  }
}
