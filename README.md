<nb-card accent="primary" class="">
    <ng-container >
     
      
      <ng-container >
  
        <nb-card-header>
          <h5 class="title-animation title-heading text-uppercase my-auto p-2">Traitement Cockpit - <span
            style="color:#DC3545">Deposer un fichier</span></h5>
        </nb-card-header>
        <nb-card-body>
          <br>
          <div class="d-flex justify-content-center box">
            <div class="w-50">
              <form>
                <nb-select  fullWidth
                           placeholder="Choisir un processus"
                >
                  <nb-option *ngFor="let option of options" [value]="option">
                    {{ option }}
                  </nb-option>
                </nb-select>
              </form>
            </div>
            <div
                class="upload-container">
              <div class="upload-box" id="drop-area">
                <label for="fileElem" class="m-auto w-100">
                    <input type="file" id="fileElem" #fileElem
                    accept=".csv, application/vnd.openxmlformats-officedocument.spreadsheetml.sheet, application/vnd.ms-excel"
             >
                  <div class="upload-box-img">
                    <nb-icon icon="file-excel-2-line">
                    </nb-icon>
                  </div>
                  <div class="upload-box-description">
                    <p>Faites glisser et déposez un fichier ici ou <span class="fw-bold text-danger">choisissez un fichier</span><br>
                      <span class="text-muted">Xlsx, xls (10 MB Max) par fichier</span></p>
                  </div>
                </label>
              </div>
              <br>
              <div *ngIf="isUploading" class="upload-progess d-flex justify-content-between shadow d-flex flex-row">
                <div class=" col-lg-1 upload-progress-icon my-auto">
                  <nb-icon icon="file-text-line"></nb-icon>
                </div>
                <div class="col-md-8 col-lg-10 upload-progress-values my-auto">
                  <div class="file-name fw-bold"></div>
                  <div class="file-size text-muted"></div>
                  <div class="progress-bar w-100">
                    <div [ngStyle]="{width: (progress)+'%'}" class="progress-bar-value"></div>
                  </div>
                </div>
                <div  class="col-lg-1 cancel-icon my-auto">
                  <nb-icon icon="close-line"></nb-icon>
                </div>
              </div>
            </div>
          </div>
        </nb-card-body>
      
      </ng-container>
    </ng-container>
  </nb-card>
  
  import { Component } from '@angular/core';
import { FormBuilder, FormGroup } from '@angular/forms';
import { NbWindowService } from '@nebular/theme';
import { Subscription } from 'rxjs';
import { Projet } from 'src/app/@core/models/project.model';
import { UtilsService } from 'src/app/@core/services/utils.service';
import { AppDataState, DataStateEnum } from 'src/app/@core/state';

@Component({
  selector: 'app-deposer-fichier',
  templateUrl: './deposer-fichier.component.html',
  styleUrls: ['./deposer-fichier.component.scss']
})
export class DeposerFichierComponent {
  options= ['Extract du Suivi des Portefeuilles Projets', 
    'Sciforma Suivi des Timesheets SGMA', 
    'P2P du Suivi des Engagements'];
  allOptions = ['Extract du Suivi des Portefeuilles Projets', 
    'Sciforma Suivi des Timesheets SGMA', 
    'P2P du Suivi des Engagements'];
  selectedOption: string = '';
  loading = false;
  progress = 0;

  readonly DataStateEnum = DataStateEnum;
  submitted = false;
  disableUpload = true;

  // Subscriptions
  formSubscription?: Subscription;
  fileSendSubscription?: Subscription
  isUploading: boolean = false;
  selectedFile!: File

  constructor(
              private fb: FormBuilder,
              private utilsService: UtilsService,
              private windowService: NbWindowService) {
  }

  ngOnInit(): void {
  }





  sendFile() {
 
  }

 
  ngOnDestroy(): void {
    this.fileSendSubscription?.unsubscribe()
    this.formSubscription?.unsubscribe()
  }

  onRecordChange(){
    this.checkPermissions();
  }


  checkPermissions() {
   
  }


}
