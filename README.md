<div style="min-width: 700px; max-width:700px; content: ''">
    <ng-container *ngIf="formGroup" class="container">
      <form id="formGroup" autocomplete="off" [formGroup]="formGroup" novalidate>
  
        <div class="d-flex justify-content-around my-2 mx-auto p-2">
          <div class="w-50">
            <label class="col-form-label my-auto">Rôle : <span class="text-danger">*</span> </label>
          </div>
          <div class="w-50 position-relative">
            <nb-select
              multiple
              fullWidth
              id="roles"
              formControlName="roles"
              placeholder="Sélectionnez les rôles approprié"
              selectrequired
              [ngClass]="{ 'status-danger': submitted && form['roles']?.errors }"
            >
              <nb-option *ngFor="let param of roles$ | async" [value]="param.id">
                {{ param.name }}
              </nb-option>
            </nb-select>
            <div *ngIf="submitted && form['roles']?.errors?.['required']"
                 class="invalid-feedback d-block position-absolute">
              Veuillez sélectionner au moins un rôle
            </div>
          </div>
        </div>
  
        <div class="d-flex justify-content-around my-2 mx-auto p-2">
          <div class="w-50">
            <label class="col-form-label my-auto">Nom : <span class="text-danger">*</span> </label>
          </div>
          <div class="w-50">
            <input
              fullWidth
              class="form-control text-uppercase"
              nbInput
              name="lastName"
              id="lastName"
              formControlName="lastName"
              required
              [ngClass]="{ 'status-danger': submitted && form['lastName']?.errors }"
            />
            <div *ngIf="submitted && form['lastName']?.errors?.['required']"
                 class="invalid-feedback d-block position-absolute">
              Champs obligatoire non renseigné
            </div>
          </div>
        </div>
  
        <div class="d-flex justify-content-around my-2 mx-auto p-2">
          <div class="w-50">
            <label class="col-form-label my-auto">Prénom : <span class="text-danger">*</span> </label>
          </div>
          <div class="w-50">
            <input
              type="text"
              fullWidth
              class="form-control text-capitalize"
              nbInput
              name="firstName"
              id="firstName"
              formControlName="firstName"
              required
              [ngClass]="{ 'status-danger': submitted && form['firstName']?.errors }"
            />
            <div *ngIf="submitted && form['firstName']?.errors?.['required']"
                 class="invalid-feedback d-block position-absolute">
              Champs obligatoire
            </div>
          </div>
        </div>
  
        <div class="d-flex justify-content-around my-2 mx-auto p-2">
          <div class="w-50">
            <label class="col-form-label my-auto">Matricule : <span class="text-danger">*</span> </label>
          </div>
          <div class="w-50">
            <input
              type="text"
              fullWidth
              class="form-control"
              nbInput
              name="matricule"
              id="matricule"
              formControlName="matricule"
              required
              [ngClass]="{ 'status-danger': submitted && form['matricule']?.errors }"
            />
            <div *ngIf="submitted && form['matricule']?.errors?.['required']"
                 class="invalid-feedback d-block position-absolute">
              Champs obligatoire non renseigné
            </div>
          </div>
        </div>
  
        <div class="d-flex justify-content-around my-2 mx-auto p-2">
          <div class="w-50">
            <label class="col-form-label my-auto">E-mail : <span class="text-danger">*</span> </label>
          </div>
          <div class="w-50">
            <input
              type="email"
              fullWidth
              class="form-control text-lowercase"
              nbInput
              name="email"
              id="email"
              formControlName="email"
              required
              [ngClass]="{ 'status-danger': submitted && form['email']?.errors }"
            />
            <div *ngIf="submitted && form['email']?.errors?.['required']"
                 class="invalid-feedback d-block position-absolute">
              Champs obligatoire non renseigné
            </div>
            <div *ngIf="submitted && form['email']?.errors?.['email']" class="invalid-feedback d-block position-absolute">
              E-mail invalide
            </div>
          </div>
        </div>
  
        <br/>
  
        <div class="form-row d-flex justify-content-end">
          <div class="form-group px-2 d-flex">
            <button *ngIf="mode === 'CREATE' || mode === 'UPDATE'" type="push" nbButton outline status="danger" class="mx-3"
                    (click)="onReset()">
              Réinitialiser
            </button>
  
            <button type="submit" class="d-flex btn btn-dark mr-3">
              <div (click)="onSave()" class="editUser">Confirmer</div>
            </button>
          </div>
        </div>
      </form>
    </ng-container>
  </div>
   ----------------------------------------------------------------------------------------------------------------------------------------------------------------



   import {Component, Input, OnInit} from '@angular/core';
import {CockpitRole, Role, User} from "../../../../core/models/user.model";
import {FormBuilder, FormGroup, Validators} from "@angular/forms";
import {UtilsService} from "../../../../core/services/utils.service";
import {NbToastrService, NbWindowRef} from "@nebular/theme";
import {UserApiService} from "../../../../core/services/user-api.service";
import {Observable, tap} from "rxjs";
import { GestionutilisateurService } from '../../services/gestion-utilisateur.service';

@Component({
  selector: 'rpa-portal-dialog-crud-users',
  templateUrl: './dialog-crud-users.component.html',
  styleUrls: ['./dialog-crud-users.component.scss']
})
export class DialogCrudUsersComponent implements OnInit {

  @Input()
  id?: number;

  @Input() user: User | undefined;
  @Input() roles: CockpitRole[] | undefined;

  mode?: 'UPDATE' | 'CREATE' | 'READ';

  formGroup!: FormGroup;
  submitted = false;
  roles$: Observable<CockpitRole[]> | undefined;
  selectedItems = [];

  constructor(
    private toastrService: NbToastrService,
    private utilsService: UtilsService,
    private utilisateurService: UserApiService,
    private gestionUtilisateurService : GestionutilisateurService,
    private fb: FormBuilder,
    private nbWindowRef: NbWindowRef
  ) {
  }

  ngOnInit(): void {
    this.resolveMode();
    if (this.mode === 'UPDATE' && this.id) {
      this.gestionUtilisateurService.getUser(this.id).subscribe((user) => {
        this.initForm(user);
        this.roles$ = this.gestionUtilisateurService.fetchRoles().pipe(tap((_) => this.formGroup?.get('roles')?.setValue(user?.rolesIds)));
      });
    }
    if (this.mode === 'CREATE') {
      this.initForm();
      this.roles$ = this.gestionUtilisateurService.fetchRoles();
    }
    if (this.user) {
      this.initForm(this.user, this.roles); 
    }

  }

  private resolveMode(): void {
    this.mode = this.id != null ? 'UPDATE' : 'CREATE';
  }

  private initForm(user?: User, role?:CockpitRole[] | undefined): void {
    this.formGroup = this.fb.group({
      enabled: [user?.enabled === false ? '0' : '1', Validators.required],
      firstName: [user?.firstName || '', Validators.required],
      lastName: [user?.lastName || '', Validators.required],
      matricule: [user?.matricule || '', Validators.required],
      email: [user?.email || '', Validators.compose([Validators.email, Validators.required])],
      roles: [user?.rolesIds || [], Validators.required], // Assuming rolesIds is an array
    });
  }
  

  get form() {
    return this.formGroup?.controls;
  }

  onReset() {
    this.submitted = false;
    this.formGroup.reset();
  }

  onSave() {
    const subTitleSucces = 'Succès';
    const titleSucces = 'Utilisateur mis à jour avec succès';
    const subTitleError = 'Erreur';
    const titleError = 'Merci de compléter les champs indiquées';

    this.submitted = true;

    // stop here if form is invalid
    if (this.formGroup.invalid) {
      this.utilsService.displayError(subTitleError, titleError);
      return;
    }

    /*const formValue = this.formGroup.getRawValue();
  let user: User = {
    ...formValue,
    rolesIds: formValue.roles, // Assign selected roles to rolesIds
    enabled: formValue.enabled == '1', // Convert string to boolean
    roles: null,  // Remove roles property
  };*/

  const formValue = this.formGroup.getRawValue();
  const user: User = {
    enabled: formValue.enabled === '1',
    firstName: formValue.firstName,
    lastName: formValue.lastName,
    matricule: formValue.matricule,
    email: formValue.email,
    rolesIds: formValue.roles,
  };

  let observable: Observable<any>;
  console.log('Mode:', this.mode);
  console.log('ID:', this.id);

  if (this.mode === 'UPDATE' && this.id) {
    user.id = this.id; // Set the id from the input property
    observable = this.gestionUtilisateurService.updateUser(this.id, user); // Use this.id for the update operation
    console.log("update", user);
  }
    else {
      observable = this.gestionUtilisateurService.createUser(user);
    }
    /*let id: number = user.id!;
    console.log("update",id)
   const subscription = this.mode === 'UPDATE' ? this.gestionUtilisateurService.updateUser(id ,user) : this.gestionUtilisateurService.createUser(user);*/
    
/*const subscription = this.mode === 'UPDATE' && this.id
? this.gestionUtilisateurService.updateUser(this.id, user)
: this.gestionUtilisateurService.createUser(user);
console.log("update",user)*/

observable.subscribe((data) => {
      this.utilsService.displaySucess(subTitleSucces, titleSucces);
      if (this.mode === 'CREATE') {
        this.onReset();
      }
      this.nbWindowRef?.close('SUCESS');
    });
  }

}
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


<nb-card accent="primary" class="">
    <ng-container *ngIf="(data$ | async) as result" [ngSwitch]="result.dataState">
      <ng-container *ngSwitchCase="DataStateEnum.LOADING">
        <rpa-portal-loading></rpa-portal-loading>
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
  ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------



  import {Component, OnInit} from '@angular/core';
import {catchError, debounceTime, map, Observable, of, startWith} from "rxjs";
import {AppDataState, DataStateEnum} from "../../../../core/state/state";
import {
  NbCalendarRange,
  NbGlobalPhysicalPosition,
  NbToastrService,
  NbWindowControlButtonsConfig,
  NbWindowRef,
  NbWindowService
} from "@nebular/theme";
import {FormBuilder, FormGroup} from "@angular/forms";
import {UtilsService} from "../../../../core/services/utils.service";
import {Router} from "@angular/router";
import {GestionutilisateurService} from "../../services/gestion-utilisateur.service";
import {SearchUserCriteria} from "../../../../core/models/search-user-criteria.model";
import {SearchUserResponse} from "../../../../core/models/search-user-response.model";
import {DialogCrudUsersComponent} from "../../components/dialog-crud-users/dialog-crud-users.component";
import { id } from 'date-fns/locale';
import {CockpitRole, User} from "../../../../core/models/user.model";
import { ConfirmActionComponent } from 'src/app/_shared/shared/components/confirm-action/confirm-action.component';
import { ThemeService } from 'ng2-charts';

@Component({
  selector: 'rpa-portal-list-utilisateurs',
  templateUrl: './list-utilisateurs.component.html',
  styleUrls: ['./list-utilisateurs.component.scss']
})
export class ListUtilisateursComponent implements OnInit {

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

  constructor(private gestionUtilisateurService: GestionutilisateurService, private toastrService: NbToastrService,
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

  showConfirmationModal(id: number) {
    this.windowService.open(ConfirmActionComponent, { context: { action: 'delete' } })
        .onClose.subscribe(confirmed => {
            if (confirmed) {
               this.deleteUser(id);
               console.log("aaa",id)
            }
        });
  }

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


