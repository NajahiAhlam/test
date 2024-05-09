5 unchanged chunks

Build at: 2024-05-09T09:31:15.181Z - Hash: a7d0f13fe5082555 - Time: 8901ms

./src/app/pages/gestion-utilisateur/gestion-utilisateur.module.ts:5:0-80 - Error: Module not found: Error: Package path ./components/shared/shared.module is not exported from package C:\Users\NAJAHIA\Documents\cockpitfront\cockpit-front\node_modules\@nebular\theme (see exports field in C:\Users\NAJAHIA\Documents\cockpitfront\cockpit-front\node_modules\@nebular\theme\package.json)



× Failed to compile.





---------------------------------------------------------------------------------------------------------------------
<nb-card accent="primary" class="">
    <ng-container *ngIf="(data$ | async) as result" [ngSwitch]="result.dataState">
      <ng-container *ngSwitchCase="DataStateEnum.LOADING">
        <!-- <rpa-portal-loading></rpa-portal-loading> -->
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
          <h5 class="title-animation title-heading text-uppercase my-auto p-2">Gestion Utilisateurs</h5>
          <div>
            <div class="listfetch">Éléments par page :
              <nb-select size="small" placeholder="Élément par page" selected="{{elementPerPage}}"
                         (selectedChange)="onElementPerPageChange($event)">
                <nb-option value="10">10</nb-option>
                <nb-option value="20">20</nb-option>
                <nb-option value="50">50</nb-option>
                <nb-option value="100">100</nb-option>
              </nb-select>
            </div>
          </div>
        </nb-card-header>
        <nb-card-body>
          <table class="table">
            <thead>
            <tr class="bg-danger fw-bold text-light border border-primary">
              <td (click)="sortBy('matricule')" class=" p-3 border border-end-white">
                <div class="d-flex justify-content-between">
                  <div>
                    Matricule
                  </div>
                  <div [ngClass]="(sortValue=='matricule')? 'text-white':'d-none'">
                          <span *ngIf="sortDirection">
                          <nb-icon icon="arrow-up-line"></nb-icon>
                          </span>
                    <span *ngIf="!sortDirection">
                          <nb-icon icon="arrow-down-line"></nb-icon>
                          </span>
                  </div>
  
                </div>
              </td>
              <td (click)="sortBy('lastName')" class=" p-3 border border-end-white">
                <div class="d-flex justify-content-between">
                  <div>
                    Nom
                  </div>
                  <div [ngClass]="(sortValue=='lastName')? 'text-white':'d-none'">
                            <span *ngIf="sortDirection">
                            <nb-icon icon="arrow-up-line"></nb-icon>
                            </span>
                    <span *ngIf="!sortDirection">
                            <nb-icon icon="arrow-down-line"></nb-icon>
                            </span>
                  </div>
  
                </div>
              </td>
              <td (click)="sortBy('firstName')" class=" p-3 border border-end-white">
                <div class="d-flex justify-content-between">
                  <div>
                    Prénom
                  </div>
                  <div [ngClass]="(sortValue=='firstName')? 'text-white':'d-none'">
                          <span *ngIf="sortDirection">
                          <nb-icon icon="arrow-up-line"></nb-icon>
                          </span>
                    <span *ngIf="!sortDirection">
                          <nb-icon icon="arrow-down-line"></nb-icon>
                          </span>
                  </div>
  
                </div>
              </td>
              <td (click)="sortBy('email')" class=" p-3 border border-end-white">
                <div class="d-flex justify-content-between">
                  <div>
                    Email
                  </div>
                  <div [ngClass]="(sortValue=='email')? 'text-white':'d-none'">
                        <span *ngIf="sortDirection">
                        <nb-icon icon="arrow-up-line"></nb-icon>
                        </span>
                    <span *ngIf="!sortDirection">
                        <nb-icon icon="arrow-down-line"></nb-icon>
                        </span>
                  </div>
  
                </div>
              </td>
              <td (click)="sortBy('enabled')" class=" p-3 border border-end-white">
                <div class="d-flex justify-content-between">
                  <div>
                    Statut
                  </div>
                  <div [ngClass]="(sortValue=='enabled')? 'text-white':'d-none'">
                          <span *ngIf="sortDirection">
                          <nb-icon icon="arrow-up-line"></nb-icon>
                          </span>
                    <span *ngIf="!sortDirection">
                          <nb-icon icon="arrow-down-line"></nb-icon>
                          </span>
                  </div>
  
                </div>
              </td>
              <td (click)="sortBy('dateCreation')" class=" p-3 border border-end-white">
                <div class="d-flex justify-content-between">
                  <div>
                    Date création
                  </div>
                  <div [ngClass]="(sortValue=='dateCreation')? 'text-white':'d-none'">
                          <span *ngIf="sortDirection">
                          <nb-icon icon="arrow-up-line"></nb-icon>
                          </span>
                    <span *ngIf="!sortDirection">
                          <nb-icon icon="arrow-down-line"></nb-icon>
                          </span>
                  </div>
  
                </div>
              </td>
              <td (click)="sortBy('dateStatus')" class=" p-3 border border-end-white">
                <div class="d-flex justify-content-between">
                  <div>
                    Date mise à jour
                  </div>
                  <div [ngClass]="(sortValue=='dateStatus')? 'text-white':'d-none'">
                          <span *ngIf="sortDirection">
                          <nb-icon icon="arrow-up-line"></nb-icon>
                          </span>
                    <span *ngIf="!sortDirection">
                          <nb-icon icon="arrow-down-line"></nb-icon>
                          </span>
                  </div>
  
                </div>
              </td>
              <td colspan="2" class=" p-3 border border-end-white">Actions</td>
            </tr>
            </thead> 
          <tbody>
            <tr>
              <td>
                <form [formGroup]="searchFormGroup">
                  <input
                    autocomplete="off"
                    formControlName="matricule"
                    class="form-control"
                    placeholder="Matricule"
                    (keyup)="onSearch()"
                  >
                </form>
              </td>
              <td>
                <form [formGroup]="searchFormGroup">
                  <input
                    autocomplete="off"
                    formControlName="lastName"
                    class="form-control"
                    placeholder="Nom"
                    (keyup)="onSearch()"
                  >
                </form>
              </td>
              <td>
                <form [formGroup]="searchFormGroup">
                  <input
                    autocomplete="off"
                    formControlName="firstName"
                    class="form-control"
                    placeholder="Prénom">
                </form>
              </td>
              <td>
                <form [formGroup]="searchFormGroup">
                  <input
                    autocomplete="off"
                    formControlName="email"
                    class="form-control"
                    placeholder="Email">
                </form>
              </td>
              <td>
                <form [formGroup]="searchFormGroup">
                  <nb-select placeholder="Statut" formControlName="enabled" fullWidth>
                    <nb-option value="true">Activé</nb-option>
                    <nb-option value="false">Désactivé</nb-option>
                  </nb-select>
                </form>
              </td>
              <td>
                <form [formGroup]="searchFormGroup">
                  <input nbInput class="form-control" placeholder="Date création"
                         autocomplete="off"
                         [nbDatepicker]="dateCreationInput">
                  <nb-rangepicker (rangeChange)=fetchUserByCreateDate($event) format="dd/MM/yyyy"
                                  #dateCreationInput></nb-rangepicker>
  
                </form>
              </td>
              <td>
                <form [formGroup]="searchFormGroup">
                  <input nbInput class="form-control" placeholder="Date mise à jour"
                         autocomplete="off"
                         [nbDatepicker]="dateStatutInput">
                  <nb-rangepicker (rangeChange)=fetchUserByStatusDate($event) format="dd/MM/yyyy"
                                  #dateStatutInput></nb-rangepicker>
  
                </form>
              </td>
              <td>
                <a (click)="onNewUtilisateur()" class="text-decoration-none float-right">
                  <button type="button" class="d-flex btn btn-dark mr-3">
                    <i class="ri-user-add-line"></i>
                    <div class="addNewUser">Ajouter</div>
                  </button>
                </a></td>
            </tr>
            <tr *ngFor="let c of result?.data?.content" class="text-truncate"
            >
              <td class="px-4 ">{{c.matricule}}</td>
              <td class="px-4 text-uppercase">{{c.lastName}}</td>
              <td class="px-4 text-capitalize">{{c.firstName}}</td>
              <td class="px-4 ">{{c.email}}</td>
              <td class="px-4 ">{{c.enabled === true ? 'Activé' : 'Désactivé'}}</td>
              <td class="px-4 ">{{c.dateCreation | date:"dd/MM/yyyy HH:mm"}}</td>
              <td class="px-4 ">{{c.dateModification| date:"dd/MM/yyyy HH:mm"}}</td>
  
              <td class="editIcon text-center">
                <nb-icon icon="edit-line" style="color: rgb(255, 187, 0)" (click)="onEditUtilisateur(c.id)"></nb-icon>
                <nb-icon icon="spam-3-line" style="color: red" (click)="showConfirmationModal(c.id ?? -1)"></nb-icon>             
              </td>
            </tr>
            </tbody> 
          </table>
        </nb-card-body>
        <nb-card-footer class="d-flex justify-content-center">
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
                <nb-icon icon="arrow-left-line"></nb-icon>
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
                <nb-icon icon="arrow-right-line"></nb-icon>
              </button>
            </li>
            <li class="list-group-item">
              <button (click)="onPageNumberChange(result?.data!.totalPages -1 )"
                      [ngClass]="result?.data?.last ? 'text-gray disabled border-light' : 'text-danger'" class="btn">
                <span>>></span>
              </button>
            </li>
          </ul>
        </nb-card-footer>
  
  
      </ng-container>
    </ng-container>
  </nb-card>


  ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  import { Component } from '@angular/core';
import {catchError, debounceTime, map, Observable, of, startWith} from "rxjs";
import {AppDataState, DataStateEnum} from "../../../../@core/state";
import {
  NbCalendarRange,
  NbGlobalPhysicalPosition,
  NbToastrService,
  NbWindowControlButtonsConfig,
  NbWindowRef,
  NbWindowService
} from "@nebular/theme";
import {FormBuilder, FormGroup} from "@angular/forms";
import {UtilsService} from "../../../../@core/services/utils.service";
import {Router} from "@angular/router";
import {GestionUtilisateurService} from "../../service/gestion-utilisateur.service";
import {SearchUserCriteria} from "../../../../@core/models/search-user-criteria.model";
import {SearchUserResponse} from "../../../../@core/models/search-user-response.model";
import {DialogCrudUsersComponent} from "../../component/dialog-crud-users/dialog-crud-users.component";
import { id } from 'date-fns/locale';
import {CockpitRole, User} from "../../../../@core/models/user.model";
//import { ConfirmActionComponent } from 'src/app/_shared/shared/components/confirm-action/confirm-action.component';
import { ThemeService } from 'ng2-charts';

@Component({
  selector: 'app-utilisateur-list',
  templateUrl: './utilisateur-list.component.html',
  styleUrls: ['./utilisateur-list.component.scss']
})
export class UtilisateurListComponent {

  data$: Observable<AppDataState<SearchUserResponse>> | undefined;
  errorMessage!: string;
  readonly DataStateEnum = DataStateEnum;
  elementPerPage = 10;
  pageNumber = 0;
  sortDirection = false;
  sortValue: string = "";
  positions = NbGlobalPhysicalPosition;
  searchFormGroup!: FormGroup;
  user!: User;

  constructor(private gestionUtilisateurService: GestionUtilisateurService, private toastrService: NbToastrService,
    private fb: FormBuilder, private utilsService: UtilsService,
    private router: Router,
    private windowService: NbWindowService,) {
  }
  ngOnInit(): void {
    this.initSearchForm();
    this.searchCustomerByKeyword();
  }
  searchCustomerByKeyword() {

    let searchUserCriteria: SearchUserCriteria = this.searchFormGroup.value;


  this.data$ = this.gestionUtilisateurService.searchUsers(searchUserCriteria, this.pageNumber, this.elementPerPage, this.sortValue, this.sortDirection ? 'ASC' : 'DESC').pipe(
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
      lastName: this.fb.control(""),
      firstName: this.fb.control(""),
      enabled: this.fb.control(""),
      email: this.fb.control(""),
      matricule: this.fb.control(""),
      dateStatusFrom: this.fb.control(""),
      dateStatusTo: this.fb.control(""),
      dateCreationFrom: this.fb.control(""),
      dateCreationTo: this.fb.control(""),
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
    const nbWindowRef = this.windowService.open(DialogCrudUsersComponent, {
      title: ` Ajouter un utilisateur`,
      buttons: buttonsConfig,
      context:{
        user:this.user
      }
    });

    this.initCloseListener(nbWindowRef); 
  }
  onEditUtilisateur(id: number | undefined) {
    if (id !== undefined) {
      this.gestionUtilisateurService.getUser(id).subscribe(
        user => {
          // Fetch role data for the user
          this.gestionUtilisateurService.fetchRoles().subscribe(
            roles => {
              const buttonsConfig: NbWindowControlButtonsConfig = {
                minimize: false,
                maximize: false,
                fullScreen: true,
                close: true,
              };
              
              const nbWindowRef = this.windowService.open(DialogCrudUsersComponent, {
                title: `Modifier un utilisateur`,
                context: { id: id,user, roles }, // Pass both user and role data as context
                buttons: buttonsConfig,
              });
  
              this.initCloseListener(nbWindowRef); 
             // console.log("user", user);
              console.log("role", roles);
            },
            error => {
              console.error("Error fetching roles:", error);
            }
          );
        },
        error => {
          console.error("Error fetching user:", error);
        } 
      );
    }
  }

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
      this.searchFormGroup.get('dateStatusTo')?.setValue(this.utilsService.formatDate(event.end, "yyyy-MM-dd'T'HH:mm:ss"));
    }

  }

  /*showConfirmationModal(id: number) {
    this.windowService.open(ConfirmActionComponent, { context: { action: 'delete' } })
        .onClose.subscribe(confirmed => {
            if (confirmed) {
               this.deleteUser(id);
               console.log("aaa",id)
            }
        });
  }*/

  deleteUser(id: number) {
      this.gestionUtilisateurService.toggleUserEnabled(id).subscribe({
        next:(data=>{
          let status=data.enabled?"activé":"désactive"
          this.utilsService.displaySucess("Succès","user "+status+" avec Succès")
          this.refreshUserData();
        }),
      }
    )
  }
  refreshUserData() {
    // Call the method to search for users with the existing criteria
    this.gestionUtilisateurService.searchUsers(SearchUserCriteria, this.pageNumber, this.elementPerPage, this.sortValue, this.sortDirection ? 'ASC' : 'DESC').subscribe({
      next: (response => {
        this.data$ = of({ dataState: DataStateEnum.LOADED, data: response });
      }),
      error: (error => {
         this.showToast('Une erreur technique est survenue', "Erreur", "danger")
      })
    });
  }

}
---------------------------------------------------------------------------------------------------------------------
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { UtilisateurListComponent } from './view/utilisateur-list/utilisateur-list.component';
import { DialogCrudUsersComponent } from './component/dialog-crud-users/dialog-crud-users.component';
import { NbCardModule, NbIconModule, NbRadioModule, NbSelectModule } from '@nebular/theme';
import { NbSharedModule } from '@nebular/theme/components/shared/shared.module';


@NgModule({
  declarations: [
    UtilisateurListComponent,
    DialogCrudUsersComponent
  ],
  imports: [
    CommonModule,
    NbSharedModule,
    NbRadioModule,
    NbCardModule,
    NbRadioModule,
    NbSelectModule,
    NbIconModule,
   
  ]
})
export class GestionUtilisateurModule { }
-------------------------------------------------------------------------
Can't bind to 'formGroup' since it isn't a known property of 'form'.ngtsc(-998002)
utilisateur-list.component.ts(25, 4): Error occurs in the template of component UtilisateurListComponent.
Can't bind to 'nbDatepicker' since it isn't a known property of 'input'.ngtsc(-998002)
'nb-rangepicker' is not a known element:
1. If 'nb-rangepicker' is an Angular component, then verify that it is part of this module.
2. If 'nb-rangepicker' is a Web Component then add 'CUSTOM_ELEMENTS_SCHEMA' to the '@NgModule.schemas' of this component to suppress this message.ngtsc(-998001)
utilisateur-list.component.ts(25, 4): Error occurs in the template of component UtilisateurListComponent.
