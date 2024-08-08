<nb-card accent="primary" class="">
  <nb-card-header>
    <h5 class="title-animation title-heading text-uppercase my-auto p-2">Traitement Cockpit - 
      <span style="color:#DC3545">Deposer un fichier</span>
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
            <input type="file" id="fileElem" #fileElem (change)="onFileChange($event)" 
              accept=".csv, application/vnd.openxmlformats-officedocument.spreadsheetml.sheet, application/vnd.ms-excel">
            <div class="upload-box-img">
              <nb-icon icon="file-excel-2-line"></nb-icon>
            </div>
            <div class="upload-box-description">
              <p>Faites glisser et déposez un fichier ici ou 
                <span class="fw-bold text-danger">choisissez un fichier</span><br>
                <span class="text-muted">Xlsx, xls (10 MB Max) par fichier</span>
              </p>
            </div>
          </label>
        </div>
        <br>
        <div *ngIf="isUploading" class="upload-progess d-flex justify-content-between shadow d-flex flex-row">
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
            <nb-icon icon="close-line"></nb-icon>
          </div>
        </div>
      </div>
    </div>
  </nb-card-body>
</nb-card>
--------------------
import { Component } from '@angular/core';
import { FormBuilder } from '@angular/forms';
import { NbWindowService } from '@nebular/theme';
import { Subscription } from 'rxjs';
import { ProjetService } from 'src/app/@core/services/projet.service';
import { FactureService } from 'src/app/@core/services/facture.service';
import { UtilsService } from 'src/app/@core/services/utils.service';

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
    private projetService: ProjetService,
    private factureService: FactureService
  ) { }

  onProcessChange(event: any) {
    this.selectedOption = event.value;
    this.checkPermissions();
  }

  onFileChange(event: Event) {
    const input = event.target as HTMLInputElement;
    if (input.files?.length) {
      this.selectedFile = input.files[0];
      this.fileSize = (this.selectedFile.size / (1024 * 1024)).toFixed(2) + ' MB';
      this.uploadFile();
    }
  }

  uploadFile() {
    if (!this.selectedFile) return;

    this.isUploading = true;
    const formData = new FormData();
    formData.append('file', this.selectedFile);

    switch (this.selectedOption) {
      case 'Extract du Suivi des Portefeuilles Projets':
        this.fileSendSubscription = this.projetService.uploadProjetFile(formData).subscribe(
          event => this.handleUploadProgress(event),
          error => this.handleError(error),
          () => this.handleComplete()
        );
        break;
      case 'Sciforma Suivi des Timesheets SGMA':
        // Call appropriate service
        break;
      case 'P2P du Suivi des Engagements':
        this.fileSendSubscription = this.factureService.uploadFactureFile(formData).subscribe(
          event => this.handleUploadProgress(event),
          error => this.handleError(error),
          () => this.handleComplete()
        );
        break;
      default:
        console.error('Unknown process selected');
        this.isUploading = false;
    }
  }

  handleUploadProgress(event: any) {
    // Update progress and handle file upload progress
    this.progress = Math.round((100 * event.loaded) / event.total);
  }

  handleError(error: any) {
    console.error('Upload error:', error);
    this.isUploading = false;
  }

  handleComplete() {
    console.log('Upload complete');
    this.isUploading = false;
    this.selectedFile = null as any;
    this.progress = 0;
  }

  cancelUpload() {
    // Cancel upload logic if applicable
    this.isUploading = false;
  }

  ngOnDestroy(): void {
    this.fileSendSubscription?.unsubscribe();
  }

  checkPermissions() {
    // Implement permission check logic here
  }
}
