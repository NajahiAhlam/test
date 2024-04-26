import { Component, Input, OnInit } from '@angular/core';
import { FormBuilder, FormGroup, Validators } from '@angular/forms';
import { NbToastrService, NbWindowRef } from '@nebular/theme';
import { Observable } from 'rxjs';
import { Programme } from 'src/app/core/models/programme.model';
import { UtilsService } from 'src/app/core/services/utils.service';
import { GestionProgrammesService } from '../../services/gestion-programmes.service';

@Component({
  selector: 'app-dialog-crud-programmes',
  templateUrl: './dialog-crud-programmes.component.html',
  styleUrls: ['./dialog-crud-programmes.component.scss']
})
export class DialogCrudProgrammesComponent implements OnInit {
  @Input()
  id?: number;

  @Input() programme: Programme | undefined;

  mode?: 'UPDATE' | 'CREATE' | 'READ';

  formGroup!: FormGroup;
  submitted = false;
  //roles$: Observable<CockpitRole[]> | undefined;
  selectedItems = [];
  

  constructor(private toastrService: NbToastrService,
    private utilsService: UtilsService,
    //private utilisateurService: UserApiService,
    private gestionProgrammeService : GestionProgrammesService,
    private fb: FormBuilder,
    private nbWindowRef: NbWindowRef) { }

  ngOnInit(): void {
    this.resolveMode();
    if (this.mode === 'UPDATE' && this.id) {
      this.gestionProgrammeService.getProgramme(this.id).subscribe((programme) => {
      });
    }
   
    if (this.programme) {
      this.initForm(this.programme); 
    }
  }

  private resolveMode(): void {
    this.mode = this.id != null ? 'UPDATE' : 'CREATE';
  }

  private initForm(programme?: Programme): void {
    this.formGroup = this.fb.group({
      firstName: [programme?.nom || '', Validators.required],
      lastName: [programme?.description || '', Validators.required],
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
  const programme: Programme = {
    nom: formValue.nom,
    description: formValue.description,
  };

  let observable: Observable<any>;
  console.log('Mode:', this.mode);
  console.log('ID:', this.id);

  if (this.mode === 'UPDATE' && this.id) {
    programme.id = this.id; // Set the id from the input property
    observable = this.gestionProgrammeService.updateUser(this.id, programme); // Use this.id for the update operation
    console.log("update", this.programme);
  }
    else {
      observable = this.gestionProgrammeService.createUser(programme);
    }

observable.subscribe((data) => {
      this.utilsService.displaySucess(subTitleSucces, titleSucces);
      if (this.mode === 'CREATE') {
        this.onReset();
      }
      this.nbWindowRef?.close('SUCESS');
    });
  }


}



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
import { GestionProgrammesService } from '../../services/gestion-programmes.service';
import {SearchUserCriteria} from "../../../../core/models/search-user-criteria.model";
import {SearchUserResponse} from "../../../../core/models/search-user-response.model";
import { DialogCrudProgrammesComponent } from '../../components/dialog-crud-programmes/dialog-crud-programmes.component';
import {SearchProgrammeResponse} from "../../../../core/models/search-programme-response.models";
import { Programme } from 'src/app/core/models/programme.model';
@Component({
  selector: 'app-list-programmes',
  templateUrl: './list-programmes.component.html',
  styleUrls: ['./list-programmes.component.scss']
})
export class ListProgrammesComponent implements OnInit {
  data$: Observable<AppDataState<SearchProgrammeResponse>> | undefined;
  errorMessage!: string;
  readonly DataStateEnum = DataStateEnum;
  elementPerPage = 10;
  pageNumber = 0;
  sortDirection = false;
  sortValue: string = "";
  positions = NbGlobalPhysicalPosition;
  searchFormGroup!: FormGroup;
  programme!: Programme;

  constructor(private gestionProgrammesService: GestionProgrammesService, private toastrService: NbToastrService,
    private fb: FormBuilder, private utilsService: UtilsService,
    private router: Router,
    private windowService: NbWindowService) { }

  ngOnInit(): void {
    this.initSearchForm();
    this.searchCustomerByKeyword();
  }
  searchCustomerByKeyword() {

    let searchUserCriteria: SearchUserCriteria = this.searchFormGroup.value;


    this.data$ = this.gestionProgrammesService.searchUsers(searchUserCriteria, this.pageNumber, this.elementPerPage, this.sortValue, this.sortDirection ? 'ASC' : 'DESC').pipe(
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
      nom: this.fb.control(""),
      description: this.fb.control(""),
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
    const nbWindowRef = this.windowService.open(DialogCrudProgrammesComponent, {
      title: ` Ajouter un programme`,
      buttons: buttonsConfig,
      context:{
        programme:this.programme
      }
    });

    this.initCloseListener(nbWindowRef); 
  }
  onEditUtilisateur(id: number | undefined) {
    if (id !== undefined) {
      this.gestionProgrammesService.getProgramme(id).subscribe(
        programme => {
          // Fetch role data for the user
          /*this.gestionProgrammesService.fetchRoles().subscribe(
            roles => {
              const buttonsConfig: NbWindowControlButtonsConfig = {
                minimize: false,
                maximize: false,
                fullScreen: true,
                close: true,
              };*/
              
              const nbWindowRef = this.windowService.open(DialogCrudProgrammesComponent, {
                title: `Modifier un programme`,
                context: { id: id,programme }, // Pass both user and role data as context
                //buttons: buttonsConfig,
              });
  
              this.initCloseListener(nbWindowRef); 
             // console.log("user", user);
              //console.log("role", roles);
            },
           
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

}
