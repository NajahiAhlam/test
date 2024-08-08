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
            <input type="file" id="fileElem" (change)="onFileChange($event)" accept=".csv, application/vnd.openxmlformats-officedocument.spreadsheetml.sheet, application/vnd.ms-excel">
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
            <nb-icon icon="close-line"></nb-icon>
          </div>
        </div>
      </div>
    </div>
  </nb-card-body>
</nb-card>
---------------
import { Component } from '@angular/core';
import { ExcelService } from 'src/app/@core/services/excel.service';
import { UtilsService } from 'src/app/@core/services/utils.service';
import { Observable, throwError } from 'rxjs';
import { catchError } from 'rxjs/operators';

@Component({
  selector: 'app-deposer-fichier',
  templateUrl: './deposer-fichier.component.html',
  styleUrls: ['./deposer-fichier.component.scss']
})
export class DeposerFichierComponent {
  progress = 0;
  selectedFile!: File;
  canUpload = true; // Update this based on your permissions logic

  constructor(private excelService: ExcelService, private utilsService: UtilsService) {}

  onFileSelected(event: Event): void {
    const target = event.target as HTMLInputElement;
    const files = target.files;

    if (files && files.length > 0) {
      this.selectedFile = files[0];
      this.uploadFile(this.selectedFile);
    }
  }

  uploadFile(file: File): void {
    const formData = new FormData();
    formData.append('file', file, file.name);

    this.excelService.uploadFile(formData).pipe(
      catchError(error => {
        console.error('Error uploading file:', error);
        return throwError(error);
      })
    ).subscribe({
      next: (data) => {
        this.utilsService.displaySuccess("Piece jointe ajoutée avec succès", "WorkFlow Cockpit");
        // Handle any post-upload logic here
        this.resetUpload();
      },
      error: (err) => {
        console.error('Upload error:', err);
        // Handle upload error
      }
    });
  }

  resetUpload() {
    // Reset file input and progress
    this.selectedFile = null as any;
    this.progress = 0;
  }
}
-----------------------
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class ExcelService {
  private uploadUrl = '/api/upload'; // Update with your API URL

  constructor(private http: HttpClient) {}

  uploadFile(formData: FormData): Observable<any> {
    return this.http.post(this.uploadUrl, formData, {
      reportProgress: true,
      observe: 'events'
    });
  }
}
--------------
uploadFile(file: File): void {
  const formData = new FormData();
  formData.append('file', file, file.name);

  let uploadObservable: Observable<any>;

  switch (this.selectedOption) {
    case 'Extract du Suivi des Portefeuilles Projets':
      uploadObservable = this.excelService.uploadFile(formData);
      break;
    case 'Sciforma Suivi des Timesheets SGMA':
      // Assign different service or URL
      break;
    case 'P2P du Suivi des Engagements':
      // Assign different service or URL
      break;
    default:
      console.error('Unknown process selected');
      return;
  }

  uploadObservable.pipe(
    catchError(error => {
      console.error('Error uploading file:', error);
      return throwError(error);
    })
  ).subscribe({
    next: (data) => {
      this.utilsService.displaySuccess("Piece jointe ajoutée avec succès", "WorkFlow Cockpit");
      this.resetUpload();
    },
    error: (err) => {
      console.error('Upload error:', err);
    }
  });
}
