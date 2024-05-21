<nb-card accent="primary" class="">
    <ng-container *ngIf="(data$ | async) as result" [ngSwitch]="result.dataState">
      <ng-container *ngSwitchCase="DataStateEnum.LOADING">
        <!-- <app-loading></app-loading>  -->
      </ng-container>
      <ng-container *ngSwitchCase="DataStateEnum.ERROR">
        <nb-card-body>
          <div class="alert alert-danger" role="alert">
            {{ result.errorMessage }}
          </div>
        </nb-card-body>
      </ng-container>
      <ng-container *ngSwitchCase="DataStateEnum.LOADED">
        <nb-card-header class="d-flex flex-row justify-content-between">
          <h5 class="title-animation title-heading text-uppercase my-auto p-2">Listes des Contrats ({{result.data?.totalElements}} Contrats)
        </h5>
          <div>
            <button type="submit" class="d-flex btn custom-bg m-1" (click)="onDownloadCSV(result.data?.content)"
                    *ngIf="result.data?.content?.length">
              exporter csv
            </button>
          </div>
        </nb-card-header>
        <nb-card-body>
            <div class="d-flex flex-row justify-content">
                  <div class="custom-context-menu">
                    <button type="submit" class="d-flex btn custom-bg m-1" id="btn-columns"  (click)="toggleContextMenu()">
                      <div class="d-flex flex-row justify-content">
                        <div class="m-1">
                          Colonnes
                        </div>
                        <div class="m-1">
                          <nb-icon [icon]="showColumnContextMenu?'arrow-up-s-line':'arrow-down-s-line'"></nb-icon>
                        </div>
                      </div>
                    </button>
                    <ul class="context-menu-box" *ngIf="showColumnContextMenu">
                      <li *ngFor="let col of columns" class="column_items"
                          (click)="showColumn(col.label)">
                        <div class="d-flex flex-row justify-content">
                          <div class="m-1">
                            <nb-icon [icon]="displayColumns.includes(col.label)?'checkbox-line':'checkbox-blank-line'"></nb-icon>
                          </div>
                          <div class="m-1">
                            {{col.label}}
                          </div>
                        </div>
                      </li>
                    </ul>
                  </div>
               
              </div>
          <div class="row m-2">
            <table class="table  w-100" *ngIf="displayColumns.length!==0">
              <thead>
                <tr class="bg-danger fw-bold text-light border border-primary">
                  <ng-container *ngFor="let column of columns">
                    <ng-container *ngIf="displayColumns.includes(column.label)">
                      <td (click)="sortBy(column.key)" class="p-3 border border-end-white">
                        <div class="d-flex justify-content-between">
                          <div style="color: white;">
                            {{ column.label }}
                          </div>
                          <div [ngClass]="(sortValue === column.label) ? 'text-black' : 'd-none'">
                            <span *ngIf="sortDirection">
                              <nb-icon icon="arrow-up-line"></nb-icon>
                            </span>
                            <span *ngIf="!sortDirection">
                              <nb-icon icon="arrow-down-line"></nb-icon>
                            </span>
                          </div>
                        </div>
                      </td>
                    </ng-container>
                  </ng-container>
                </tr>
              </thead>
              <tbody>
                <tr>
                  <ng-container *ngFor="let column of columns">
                    <ng-container *ngIf="displayColumns.includes(column.label)">
                      <td class="p-2 border border-end-white">
                        <form [formGroup]="searchFormGroup">
                          <ng-container [ngSwitch]="column.label">
                              <input *ngSwitchDefault nbInput autocomplete="off" formControlName={{column.label}} class="form-control" placeholder={{column.label}} type="text" (keyup)="onSearch()">
                          </ng-container>

                          </form>
                        </td>
                        </ng-container>
                    </ng-container>
                </tr>
                <ng-template [ngIf]="result.data?.content?.length">
                     <tr *ngFor="let c of result.data?.content" (click)="goToDetails(c)">
                                <td class="px-4 text-capitalize" *ngIf="displayColumns.includes('Fournisseur')">{{c.fournisseur?.nom}}</td>
                                <td class="px-4 text-capitalize" *ngIf="displayColumns.includes('User')">{{c.user.firstName}}</td>
                                <td class="px-4 text-capitalize" *ngIf="displayColumns.includes('Ref Contrat')">{{c.refContrat}}</td>
                                <td class="px-4" *ngIf="displayColumns.includes('Type')">{{c.type}}</td>
                                <td class="px-4" *ngIf="displayColumns.includes('Date de creation')">{{c.dateCreation | date:"dd/MM/yyyy HH:mm"}}</td>
                                <td class="px-4" *ngIf="displayColumns.includes('Status')">{{c.status}}</td>
                                <td class="px-4" *ngIf="displayColumns.includes('Commantaire')">{{c.commentaire}}</td>
                                <td class="px-4 text-capitalize" *ngIf="displayColumns.includes('Correspondant')">{{c.correspondant}}</td>
                                <td class="px-4 text-capitalize" *ngIf="displayColumns.includes('Date Validite KYS')">{{c.dateValiditeKYS}}</td>
                                <td class="px-4" *ngIf="displayColumns.includes('Lead Contrat')">{{c.leadContrat}}</td>
                                <td class="px-4" *ngIf="displayColumns.includes('Date de modification')">{{c.dateModification | date:"dd/MM/yyyy HH:mm"}}</td>
                                <td class="px-4 text-capitalize" *ngIf="displayColumns.includes('Nature Contrat')">{{c.natureContrat}}</td>
                                <td class="px-4" *ngIf="displayColumns.includes('Date Signature Contrat')">{{c.dateSignatureContrat | date:"dd/MM/yyyy HH:mm"}}</td>
                                <td class="px-4 text-capitalize" *ngIf="displayColumns.includes('Contrat Initial')">{{c.ContratInitial}}</td>
                                <td class="px-4" *ngIf="displayColumns.includes('Date Validite Contrat')">{{c.dateValiditeContrat | date:"dd/MM/yyyy HH:mm"}}</td>
                                <td class="px-4 text-capitalize" *ngIf="displayColumns.includes('Frequence Paiement')">{{c.frequencePaiement}}</td>
                                <td class="px-4 text-capitalize" *ngIf="displayColumns.includes('Frequence Paiement Nom')">{{c.frequencePaiementNom}}</td>
                                <td class="px-4 text-capitalize" *ngIf="displayColumns.includes('Montant HT')">{{c.montantHt}}</td>
                                <td class="px-4" *ngIf="displayColumns.includes('Delais Paiement')">{{c.delaisPaiement}}</td>
                                <td class="px-4 text-capitalize" *ngIf="displayColumns.includes('Devise')">{{c.devise}}</td>
                                <td class="px-4 text-capitalize" *ngIf="displayColumns.includes('File')">{{c.file}}</td>                               
                    </tr>
                        </ng-template>
                        <ng-template [ngIf]="!result.data?.content?.length">
                            <tr>
                                <td colspan="100%">Aucune Contrat trouvée pour le moment</td>
                            </tr>
                        </ng-template>
              </tbody>
            </table>
          </div>
        </nb-card-body>
        <nb-card-footer class="d-flex justify-content-between">
            <ul class="list-group list-group-horizontal">
              <li class="list-group-item">
                <button (click)="onPageNumberChange(0)"
                        [ngClass]="result?.data?.first ? 'text-gray disabled border-light' : 'text-danger'" class="btn">
                  <span><<</span>
                </button>
              </li>
              <li class="list-group-item">
                <button (click)="onPageNumberChange(pageNumber-1)"
                        [ngClass]="result?.data?.first ? 'text-gray disabled border-light' : 'text-danger'" class="btn">
                  <nb-icon icon="arrow-circle-left-outline"></nb-icon>
                </button>
              </li>
              <li class="list-group-item text-truncate"
                  *ngFor='let nbr of toTotalPages(result?.data!.totalPages <9 ? result?.data!.totalPages : 9) ;let i = index'>
    
                <span
                  *ngIf="(result?.data!.number <= result?.data!.totalPages-9) || (result?.data!.totalPages <9);else printlastPages">
                  <button (click)="onPageNumberChange(result?.data!.number <= 5 ? i : result?.data!.number-4+i)"
                          [ngClass]="pageNumber==(result?.data!.number <= 5 ? i : result?.data!.number-4 + i) ?'text-light bg-danger' : 'text-danger'"
                          class="btn ">
                    {{(result?.data!.number <= 5 ? 1 + i : result?.data!.number - 3 + i)}}
                  </button>
                </span>
                <ng-template #printlastPages>
                  <button (click)="onPageNumberChange(result?.data!.totalPages - 9 + i)"
                          [ngClass]="pageNumber==(result?.data!.totalPages - 9 + i) ?'text-light bg-danger' : 'text-danger'"
                          class="btn ">
                    {{(result?.data!.totalPages - 8 + i)}}
                  </button>
                </ng-template>
    
              </li>
              <li class="list-group-item">
                <button (click)="onPageNumberChange(pageNumber+1)"
                        [ngClass]="result?.data?.last ? 'text-gray disabled border-light' : 'text-danger'" class="btn">
                  <nb-icon icon="arrow-circle-right-outline"></nb-icon>
                </button>
              </li>
              <li class="list-group-item">
                <button (click)="onPageNumberChange(result?.data!.totalPages -1 )"
                        [ngClass]="result?.data?.last ? 'text-gray disabled border-light' : 'text-danger'" class="btn">
                  <span>>></span>
                </button>
              </li>
            </ul>
            <div>
              <div >Éléments par page :
                <nb-select size="small" placeholder="Élément par page" selected="{{elementPerPage}}"
                           (selectedChange)="onElementPerPageChange($event)">
                  <nb-option value="10">10</nb-option>
                  <nb-option value="20">20</nb-option>
                  <nb-option value="50">50</nb-option>
                  <nb-option value="100">100</nb-option>
                </nb-select>
              </div>
            </div> 
          </nb-card-footer>
        </ng-container>
      </ng-container>
    </nb-card>
import {Component, OnInit} from '@angular/core';
import {catchError, debounceTime, map, Observable, of, startWith} from "rxjs";
import { AppDataState, DataStateEnum } from 'src/app/@core/state';
import {
  NbCalendarRange,
  NbGlobalPhysicalPosition,
  NbToastrService,
  NbWindowControlButtonsConfig,
  NbWindowRef,
  NbWindowService,
  
} from "@nebular/theme";
import {FormBuilder, FormGroup, } from "@angular/forms";
import { UtilsService } from 'src/app/@core/services/utils.service';
import {Router} from "@angular/router";
import { GestionContratService } from '../../services/gestion-contrat.service';
import { SearchUserCriteria } from 'src/app/@core/models/search-user-criteria.model';
import { DialogContratComponent } from '../../component/dialog-contrat/dialog-contrat.component';
import { Programme } from 'src/app/@core/models/programme.model';
import { ObjectPagination } from 'src/app/@core/models/object.pagination.model';
import { Contrat, ContratInitial } from 'src/app/@core/models/contrat.model';
import * as Papa from "papaparse";
import * as downloadjs from "downloadjs";
@Component({
  selector: 'app-list-contrat',
  templateUrl: './list-contrat.component.html',
  styleUrls: ['./list-contrat.component.scss']
})
export class ListContratComponent {
  
  data$: Observable<AppDataState<ObjectPagination<Contrat>>> | undefined;
  errorMessage!: string;
  readonly DataStateEnum = DataStateEnum;
  elementPerPage = 10;
  pageNumber = 0;
  sortDirection = false;
  sortValue: string = "";
  positions = NbGlobalPhysicalPosition;
  searchFormGroup!: FormGroup;
  programme!: Programme;
  displayColumns: Array<string>=['Fournisseur','User','Ref Contrat','Type',"Date de creation",'Date Signature Contrat','Status','Nature Contrat'];

  columns = [
    { key: 'fournisseur', label: 'Fournisseur' },
    { key: 'user', label: 'User' },
    { key: 'refContrat', label: 'Ref Contrat' },
    { key: 'type', label: "Type" },
    { key: 'dateCreation', label: 'Date de creation' },
    { key: 'status', label: 'Status' },
    { key: 'commantaire', label: 'Commantaire' },
    { key: 'correspondant', label: 'Correspondant' },
    { key: 'dateValiditeKYS', label: 'Date Validite KYS' },
    { key: 'leadContrat', label: 'Lead Contrat' },
    { key: 'DateModification', label: 'Date de modification' },
    { key: 'datureContrat', label: 'Nature Contrat' },
    { key: 'dateSignatureContrat', label: 'Date Signature Contrat' },
    { key: 'contratInitial', label: 'Contrat Initial' },
    { key: 'dateValiditeContrat', label: 'Date Validite Contrat' },
    { key: 'frequencePaiement', label: 'Frequence Paiement' },
    { key: 'frequencePaiement Nom', label: 'Frequence Paiement Nom' },
    { key: 'delaisPaiement', label: 'Delais Paiement' },
    { key: 'montantHt', label: 'Montant Ht' },
    { key: 'devise', label: 'Devise' },
    { key: 'file', label: 'File' }
];


showColumnContextMenu: boolean=false;


constructor(private gestionContratService: GestionContratService, private toastrService: NbToastrService,
  private fb: FormBuilder, private utilsService: UtilsService,
  private router: Router,
  private windowService: NbWindowService) { }

  ngOnInit(): void {
    console.log(this.displayColumns.includes("Contrat Initial"))
    console.log('gggg',this.columns)
     this.initSearchForm();
     const storedColumns = localStorage.getItem("columns");
   if (storedColumns) {
    this.displayColumns = JSON.parse(storedColumns);
  } else {
    localStorage.setItem("columns", JSON.stringify(this.displayColumns));
  }
    this.searchCustomerByKeyword();
  }
  searchCustomerByKeyword() {

    let searchUserCriteria: SearchUserCriteria = this.searchFormGroup.value;


    this.data$ = this.gestionContratService.searchContrats(searchUserCriteria, this.pageNumber, this.elementPerPage, this.sortValue, this.sortDirection ? 'ASC' : 'DESC').pipe(
      map(response => {
        return ({dataState: DataStateEnum.LOADED, data: response})
      }),
      startWith({dataState: DataStateEnum.LOADING}),
      catchError(err => of({
        dataState: DataStateEnum.ERROR,
        errorMessage: err.message,
        this: this.showToast('Une erreur technique est survenue', "Erreur", "danger")
      }))
    )
  }
  toggleContextMenu() {
    this.showColumnContextMenu = !this.showColumnContextMenu;
  }

  showColumn(col: string) {
    console.log(col);
    if(!this.displayColumns.includes(col))
      this.displayColumns.push(col)
    else
      this.displayColumns.splice(this.displayColumns.indexOf(col),1)
    const storedColumns = localStorage.getItem("columns");
    console.log(storedColumns)
      localStorage.setItem("columns", JSON.stringify(this.displayColumns));
  }


  

  sortBy(value: string) {
    if (this.sortValue == value.toString()) {
      this.sortDirection = !this.sortDirection;
    } else {
      this.sortDirection = true;
      this.sortValue = value;
    }
    this.searchCustomerByKeyword();
  }


  showToast(message: string, title: string, status: string) {
    return this.toastrService.show(message, title, {status, duration: 0});
  }

  toTotalPages(i: number) {
    return new Array(i);
  }

  onElementPerPageChange(event: string) {
    this.elementPerPage = +event;
    this.pageNumber = 0;
    this.searchCustomerByKeyword();

  }

  onPageNumberChange(event: number) {
    this.pageNumber = event;
    this.searchCustomerByKeyword();
  }

  onSearch() {
    this.searchFormGroup.valueChanges.pipe(
      debounceTime(1000)).subscribe(() => {
       this.searchCustomerByKeyword();
      }
    );

  }

  initSearchForm() {
    this.searchFormGroup = this.fb.group({
      fournisseur: [''], 
      user: [''], 
      refContrat: [''], 
      natureContrat: [''], 
      correspondant: [''], 
      dateValiditeKYS: [''], 
      ContratInitial: [''], 
      type: [''], 
      status: [''], 
      commentaire: [''], 
      dateSignatureContrat: [''], 
      dateValiditeContrat: [''], 
      delaisPaiement: [''], 
      leadContrat: [''], 
      dateCreation: [''], 
      dateModification: [''], 
      frequencePaiement: [''], 
      frequencePaiementNom: [''], 
      montantHt: [''], 
      devise: [''], 
      file: [''] 
    });
    this.onSearch();

  }

  onNewUtilisateur() {
    const buttonsConfig: NbWindowControlButtonsConfig = {
      minimize: false,
      maximize: false,
      fullScreen: true,
      close: true,
    };
    const nbWindowRef = this.windowService.open(DialogContratComponent, {
      title: ` Ajouter un contrat`,
      buttons: buttonsConfig,
    });

    this.initCloseListener(nbWindowRef); 
  }
  onEditUtilisateur(id: number | undefined) {
    if (id !== undefined) {
      this.gestionContratService.getContrat(id).subscribe(
        contrat => {
              const buttonsConfig: NbWindowControlButtonsConfig = {
                minimize: false,
                maximize: false,
                fullScreen: true,
                close: true,
              };
              
              const nbWindowRef = this.windowService.open(DialogContratComponent, {
                title: `Modifier un contrat`,
                context: { id: id,contrat }, 
                buttons: buttonsConfig,
              });
  
              this.initCloseListener(nbWindowRef); 
            },         
      );
    }
  }
  exportData(){}
  fetchUserByCreateDate(event: NbCalendarRange<Date>) {
    if (event.start && event.end) {
      this.searchFormGroup.get('dateCreationFrom')?.setValue(this.utilsService.formatDate(event.start, "yyyy-MM-dd'T'HH:mm:ss"));
      this.searchFormGroup.get('dateCreationTo')?.setValue(this.utilsService.formatDate(event.end, "yyyy-MM-dd'T'HH:mm:ss"));
    }

  }

  private initCloseListener(windowRef: NbWindowRef) {
    windowRef?.onClose.subscribe({
      next: (res) => {
        if (res === 'SUCESS') this.searchCustomerByKeyword();
      },

      error: (err) => console.error(`Observer got an error: ${err}`),
    });
  }

  fetchUserByStatusDate(event: NbCalendarRange<Date>) {
    if (event.start && event.end) {
      this.searchFormGroup.get('dateStatusFrom')?.setValue(this.utilsService.formatDate(event.start, "yyyy-MM-dd'T'HH:mm:ss"));
      this.searchFormGroup.get('dateModification')?.setValue(this.utilsService.formatDate(event.end, "yyyy-MM-dd'T'HH:mm:ss"));
    }

  }
  goToDetails(req: Contrat){
    console.log(req.id)
    this.router.navigate(['/pages/gestion-contrat/',req.id])
  }
  onDownloadCSV(_data: [Contrat] | undefined){
    if(_data)
      {
        const settings= [
          { key: 'fournisseur', label: 'Fournisseur' },
          { key: 'user', label: 'User' },
          { key: 'refContrat', label: 'Ref Contrat' },
          { key: 'type', label: "Type" },
          { key: 'dateCreation', label: 'Date de creation' },
          { key: 'status', label: 'Status' },
          { key: 'commantaire', label: 'Commantaire' },
          { key: 'correspondant', label: 'Correspondant' },
          { key: 'dateValiditeKYS', label: 'Date Validite KYS' },
          { key: 'leadContrat', label: 'Lead Contrat' },
          { key: 'DateModification', label: 'Date de modification' },
          { key: 'datureContrat', label: 'Nature Contrat' },
          { key: 'dateSignatureContrat', label: 'Date Signature Contrat' },
          { key: 'contratInitial', label: 'Contrat Initial' },
          { key: 'dateValiditeContrat', label: 'Date Validite Contrat' },
          { key: 'frequencePaiement', label: 'Frequence Paiement' },
          { key: 'frequencePaiement Nom', label: 'Frequence Paiement Nom' },
          { key: 'delaisPaiement', label: 'Delais Paiement' },
          { key: 'montantHt', label: 'Montant Ht' },
          { key: 'devise', label: 'Devise' },
          { key: 'file', label: 'File' },
        ];
        const  data=_data.map((f: any) => {
          f.fournisseur=f?.fournisseur?.name;
          f.user=f?.user?.firstName;
          f.refContrat=f?.refContrat;
          f.type=f?.type;
          f.status=f?.status;
          f.commantaire=f?.commantaire;
          f.correspondant=f?.correspondant;
          f.dateValiditeKYS=f?.dateValiditeKYS;
          f.leadContrat=f?.leadContrat;
          return f;
        });
        this.downloadCSV(this.mapDataToTable(data, settings));
    }
  }
      mapDataToTable(data: any[], columns: any[]) {
        const tableHeaders = columns.map(column => column.title);
        const tableData = data.map(row => {
          return columns.map(column => (row[column.key]));
        });

        return [tableHeaders, ...tableData];
      }

      downloadCSV(data: any) {
        if (window["Uint8Array"]) {
          const fileName = this.getDocumentTitle().concat(".csv");
          const csvData = Papa.unparse(data, { delimiter: ";", quotes: true });
          downloadjs(csvData, fileName, "text/plain");
        } else {
          alert(
            `Cette fonctionnalité n'est pas disponible sur votre navigateur!`
          );
        }
      }

      getDocumentTitle() {
        return "Historique " + Date.now();
      }


}
