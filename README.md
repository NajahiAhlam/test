// excel.service.ts (or your corresponding service file)
import { HttpClient } from '@angular/common/http';
import { Injectable } from '@angular/core';
import { Observable } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class ExcelService {
  private apiUrl = 'http://localhost:9090/api/cockpit/sciformaTimeSheets/upload';

  constructor(private http: HttpClient) {}

  uploadFile(formData: FormData): Observable<string> {
    return this.http.post(this.apiUrl, formData, { responseType: 'text' });
  }
}
import { Component } from '@angular/core';
import { FormBuilder } from '@angular/forms';
import { catchError, Observable, throwError } from 'rxjs';
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
  
    let uploadObservable: Observable<string> | undefined;
    console.log("selectedOption", this.selectedOption)
    switch (this.selectedOption) {
      case 'Extract du Suivi des Portefeuilles Projets':
        uploadObservable = this.excelService.uploadFile(formData);
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
        next: (response) => {
          // response is plain text
          this.utilsService.displaySucess(response, "WorkFlow Cockpit");
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
