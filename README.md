<nb-card style="width: 700px;">
    <nb-card-header>
      <div class="d-flex justify-content-between">
        <div>
          <span>Grille de Validation</span>
        </div>
        <div>
         <button nbButton (click)="closeWindow()"><nb-icon class="modal-icon text-dark" icon="xmark" pack="fas"></nb-icon></button>
        </div>
      </div>
    </nb-card-header>
  
    <nb-card-body style="overflow-x: hidden;">
      <form [formGroup]="form" autocomplete="off">
        <!-- Sponsor Input -->
        <div  class="d-flex justify-content-around my-2 mx-auto p-2">
          <div class="w-50">
            <div class="col-form-label my-auto mx-auto">Type de validation: <span class="text-danger">*</span></div>
          </div>
          <div class="w-50 position-relative">
            <nb-select  fullWidth
                   placeholder="Veuillez sélectionner type" formControlName="validation" autocomplete="off"
                   name="validation">
                   
              <nb-option *ngFor="let val of validations" [value]="val">
                {{ val }}
              </nb-option>
              </nb-select>
              
            <div *ngIf="submitted && form.get('validation')?.errors?.['required']" class="invalid-feedback d-block position-absolute">
              <small class="text-danger">Veuillez sélectionner type</small>
            </div>
          </div>
        </div>
  
  
  
        <!-- Comment Text Area -->
         <div class="d-flex justify-content-around my-2 mx-auto p-2" *ngIf="validationValue =='Sans Condition'">
          <div class="w-50">
            <div class="col-form-label my-auto">Commentaire:<span class="text-danger">*</span></div>
          </div>
          <div class="w-50 position-relative">
            <textarea formControlName="comment" class="form-control" name="comment"
                      placeholder="Veuillez entrer un commentaire" rows="3"></textarea>
            <div *ngIf="submitted && form.get('comment')?.errors?.['required']"
                 class="invalid-feedback d-block position-absolute">
              <small class="text-danger">Le commentaire est obligatoire</small>
            </div>
          </div>
        </div> 


        <!-- Condition form -->
              <div *ngIf="validationValue =='Avec Condition'"  style="height: 500px;">
                    <div formArrayName="conditions">
                      <div *ngFor="let condition of conditions.controls; let i = index" [formGroupName]="i" class="my-2 mx-auto p-2">
                    <div class="d-flex justify-content-around my-2 mx-auto p-2">
                    <div class="w-50">
                      <div class="col-form-label my-auto">Détail condition {{i + 1}}:</div>
                    </div>
                    <div class="w-50 position-relative">
                      <textarea formControlName="detail" fullWidth class="form-control" placeholder="Ajoutez votre detail" rows="3"></textarea>
                    </div>

                    </div>
                  
                    <div class="d-flex justify-content-around my-2 mx-auto p-2">
                        <div class="w-50">
                            <div class="col-form-label my-auto">catégorie  : <span class="text-danger">*</span></div>
                        </div>
                        <div class="w-50 position-relative">
                            <nb-select formControlName="categorie" fullWidth placeholder="Sélectionnez Niveau de risque résiduele" autocomplete="off"  name="type" id="type">
                              <nb-option *ngFor="let item of categorie" [value]="item">{{item}}</nb-option>
                            </nb-select>
                        </div>
                    </div>
                    <div  class="d-flex justify-content-around my-2 mx-auto p-2">
                        <div class="w-50">
                          <div class="col-form-label my-auto mx-auto">Assigné : <span class="text-danger">*</span></div>
                        </div>
                        <div class="w-50 position-relative">
                          <input  [nbAutocomplete]="autoAssigne" nbInput fullWidth
                                placeholder="Veuillez sélectionner Utilisateur" autocomplete="off"
                                name="assigne" formControlName="assigne">
                       <nb-autocomplete #autoAssigne>
  <nb-option *ngFor="let user of filteredAssigneOptions[i] | async" [value]="user.email">
    {{ user.email }}
  </nb-option>
</nb-autocomplete>
                        </div>
                      </div>
                      
                      <div class="d-flex justify-content-around my-2 mx-auto p-2" *ngIf="conditions.controls[i].get('categorie')?.value === 'poste condition'">
                        <div class="w-50">
                            <label class="col-form-label my-auto">nombre semaine après lancement : <span class="text-danger">*</span></label>
                        </div>
                        <div class="w-50 position-relative">
                            <input  formControlName="numberSemaineApresLancement" nbInput type="number"  fullWidth />
                        </div>
                    </div>
                    <!-- <div class="d-flex justify-content-around my-2 mx-auto p-2" *ngIf="conditions.controls[i].get('categorie')?.value === 'poste condition'">
                        <div class="w-50">
                            <label class="col-form-label my-auto">Date  de lancement : <span class="text-danger">*</span></label>
                        </div>
                        <div class="w-50 position-relative">
                          <input formControlName="dateLancement" nbInput placeholder="Sélectionnez une Date"  [nbDatepicker]="dateTimePicker" fullWidth>
                            <nb-datepicker #dateTimePicker></nb-datepicker> 
                        </div>
                    </div> -->
                    <div class="d-flex justify-content-around my-2 mx-auto p-2" *ngIf="conditions.controls[i].get('categorie')?.value === 'précondition'">
                        <div class="w-50">
                            <label class="col-form-label my-auto">Date échéance : <span class="text-danger">*</span></label>
                        </div>
                        <div class="w-50 position-relative">
                          <input formControlName="dateEcheance" nbInput placeholder="Sélectionnez une Date"  [nbDatepicker]="dateTimePickerEcheance" fullWidth>
                            <nb-datepicker #dateTimePickerEcheance></nb-datepicker> 
                        </div>
                    </div>
                    <!-- <div class="d-flex justify-content-around my-2 mx-auto p-2">
                        <div class="w-50">
                            <div class="col-form-label my-auto">Etat   : <span class="text-danger">*</span></div>
                        </div>
                        <div class="w-50 position-relative">
                            <nb-select formControlName="etat" fullWidth placeholder="Sélectionnez etat" autocomplete="off"  name="type" id="type">
                              <nb-option *ngFor="let item of etat"  [value]="item ">{{item}} </nb-option>
                            </nb-select>
                        </div>
                    </div> -->

                    <div class="d-flex justify-content-around my-2 mx-auto p-2">
                        <div class="w-50">
                          <div class="col-form-label my-auto">Commentaire :<span class="text-danger">*</span></div>
                        </div>
                        <div class="w-50 position-relative">
                          <textarea formControlName="commentCond" class="form-control" placeholder="Ajoutez votre commentaire  de l'assigné" rows="3"></textarea>
                        </div>
                      </div>
                      <div class="d-flex justify-content-end mt-2">
                        <button type="button" class="btn btn-danger" (click)="removeConditions(i)" *ngIf="conditions.controls.length >= 1">Supprimer</button>
                      </div>
    </div>

    </div>
    <button type="button" class="btn btn-secondary" *ngIf="conditions.controls.length >= 1" (click)="addCondition()">Ajouter une condition</button>
                  </div>
      </form>
    </nb-card-body>
  
    <nb-card-footer>
      <div class="d-flex flex-row justify-content-end">
        <button type="button" nbButton outline status="danger" class="mx-3" (click)="onResetForm()">
          Réinitialiser
        </button>
        <button type="submit" class="d-flex btn btn-dark mr-3" (click)="onSubmit()">
          Valider
        </button>
      </div>
    </nb-card-footer>
  </nb-card>
  
  <ng-container *ngIf="loading">
    <!-- <rpa-portal-loading></rpa-portal-loading> -->
  </ng-container>
  export class AddConditionDialogComponent {
  form!: FormGroup;
  @Input() risqueId !:number;
  
  submitted: boolean = false;
  loading: boolean = false;
  validations =  ['Avec Condition', 'Sans Condition'];
  user: any[] = [];
  filteredAssigneOptions$!: Observable<any[]>;
  etat = ['clôturé', 'en cours', 'en attente','suspendu'];
  categorie = ['précondition', 'poste condition'];
  validationValue: string='';
  filteredAssigneOptions: BehaviorSubject<any[]>[] = [];
  
    constructor(
      private formBuilder: FormBuilder,
      private _utilService: UtilsService,
      protected dialogRef: NbDialogRef<AddConditionDialogComponent>,
      private userService :UserService,
      private wkfService : WkfService,
      private risqueInstanceService: GestionRisqueService
    ) { }
  
    ngOnInit() {
      this.initForm();
  
      this.form.get('validation')?.valueChanges.subscribe(value => {
      this.validationValue = value;

        const commentCtrl = this.form.get('comment');
        const conditionsArray = this.form.get('conditions') as FormArray;

        if (value === 'Sans Condition') {
          commentCtrl?.setValidators([Validators.required]);
          commentCtrl?.updateValueAndValidity();
        } else {
          commentCtrl?.clearValidators();
          commentCtrl?.updateValueAndValidity();

          if (conditionsArray.length === 0) {
            this.addCondition();  
          }
        }
      });
  this.userService.getUsersList().subscribe(users => {
    this.user = users;
  });
console.log('user: ', this.user)
      /*   this.userService.getUsersList().subscribe(sponsors => {
          this.user = sponsors.filter(el=>el.enabled===true);
          this.filteredAssigneOptions$ = this.form.get('assign')!.valueChanges.pipe(
            startWith(''),
            map(filterString => this.filterOptions(filterString, this.user, 'email'))
          );
        }); */
    
    }
  
    initForm(risque?: RisqueInstance) {
      this.form = this.formBuilder.group({
        validation: ['', Validators.required],
        comment: [''],
          conditions: this.formBuilder.array(
          risque?.conditions?.length
            ? risque.conditions.map(c => this.createConditionGroup(c))
            : []
        ),

      });
    }
      getFormControl(control: any): FormControl {
          return control as FormControl;
        }

      createConditionGroupold(condition?: Condition): FormGroup {
            return this.formBuilder.group({
              id: [condition ? condition.id : null],
              detail: [condition ? condition.detail : '', [Validators.required]],
              categorie: [condition ? condition.categorie : '', [Validators.required]],
              assigne: ['' , [Validators.required]],
              numberSemaineApresLancement: [condition ? condition.numberSemaineApresLancement : ''],
              dateEcheance: [condition ? condition.dateEcheance :'' ],
              commentCond: ['', [Validators.required]],
            });


            
          }


          createConditionGroup(condition?: Condition): FormGroup {
  const group = this.formBuilder.group({
    id: [condition?.id ?? null],
    detail: [condition?.detail ?? '', Validators.required],
    categorie: [condition?.categorie ?? '', Validators.required],
    assigne: [condition?.assigne ?? '', Validators.required],
    numberSemaineApresLancement: [condition?.numberSemaineApresLancement ?? ''],
    dateEcheance: [condition?.dateEcheance ?? ''],
    commentCond: [condition?.commentCond ?? '', Validators.required],
  });

  // Set up conditional validators based on `categorie`
  group.get('categorie')?.valueChanges
    .pipe(startWith(group.get('categorie')?.value ?? ''))
    .subscribe((value: string | null) => {
      const dateCtrl = group.get('dateEcheance');
      const semaineCtrl = group.get('numberSemaineApresLancement');

      if (value === 'préconditionn') {
        dateCtrl?.setValidators([Validators.required]);
        semaineCtrl?.clearValidators();
      } else if (value === 'poste condition') {
        semaineCtrl?.setValidators([Validators.required]);
        dateCtrl?.clearValidators();
      } else {
        // Default: clear both
        dateCtrl?.clearValidators();
        semaineCtrl?.clearValidators();
      }

      dateCtrl?.updateValueAndValidity({ onlySelf: true });
      semaineCtrl?.updateValueAndValidity({ onlySelf: true });
    });

  return group;
}



          get conditions(): FormArray {
            return this.form.get('conditions') as FormArray;

          }
       
     addCondition(): void {
  const conditionGroup = this.createConditionGroup();
  this.conditions.push(conditionGroup);

  const index = this.conditions.length - 1;

  // Create a new BehaviorSubject for this condition
  const assigneFilter$ = new BehaviorSubject<any[]>(this.user);
  console.log("assigneFilter$: ",assigneFilter$)
  this.filteredAssigneOptions[index] = assigneFilter$;

  // Subscribe to changes in the assigne input
  conditionGroup.get('assigne')?.valueChanges
    .pipe(startWith(''))
    .subscribe(value => {
      const filtered = this.filterOptions(value || '', this.user, 'email');
      assigneFilter$.next(filtered);
    });
}     
        
          removeConditions(index: number): void {
            this.conditions.removeAt(index);
          }
        
  
filterOptions(filterString: string, options: any[], field: string): any[] {
  const filterValue = filterString.toLowerCase();
  return options.filter(option => option[field].toLowerCase().includes(filterValue));
}
  
    closeWindow() {
      this.dialogRef.close();
    }
  
    onResetForm() {
      this.form.reset();
    }


      onSubmit() {
          this.submitted = true;
      this.loading = true;
    
      // Check if the form is invalid
      if (this.form.invalid) {
        this._utilService.displayWarning("Veuillez remplir tous les champs", "MYCNP");
        this.loading = false;
        return;
      }
  const formValue = this.form.value;

  const payload: RisqueValidationRequest = {
    conditions: formValue.conditions && formValue.conditions.length > 0 ? formValue.conditions : [],
    comment: formValue.comment,
    typeValidation: formValue.validation
  };

  this.risqueInstanceService.validerRisque(this.risqueId, payload).subscribe({
    next: (updatedRisque) => {
     this._utilService.displaySucess("Votre validation a été ajoutée avec succès", "MYCNP");
        this.dialogRef.close("SUCCESS");
    },
    error: (err) => {
        this._utilService.displayError(err.errorMessage, "Erreur");
      // Afficher message d'erreur
    }
  });

      
}
  

}
