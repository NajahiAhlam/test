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

    let uploadObservable: Observable<any>;

    switch (this.selectedOption) {
      case 'Extract du Suivi des Portefeuilles Projets':
        uploadObservable = this.excelService.uploadFilee(formData);
        break;
      case 'Sciforma Suivi des Timesheets SGMA':
        // Replace with appropriate method
        uploadObservable = this.excelService.uploadSciforma(formData);
        break;
      case 'P2P du Suivi des Engagements':
        // Replace with appropriate method
        uploadObservable = this.excelService.uploadP2P(formData);
        break;
      default:
        console.error('Unknown process selected');
        return;
    }

    this.isUploading = true;

    uploadObservable.pipe(
      catchError(error => {
        console.error('Error uploading file:', error);
        this.handleError(error);
        return throwError(error);
      })
    ).subscribe({
      next: (data) => {
        this.utilsService.displaySucess("Piece jointe ajoutée avec succès", "WorkFlow Cockpit");
        this.handleComplete();
      },
      error: (err) => {
        console.error('Upload error:', err);
        this.handleError(err);
      }
    });
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
    // Logic to cancel upload if applicable
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
