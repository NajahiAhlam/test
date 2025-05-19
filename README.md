export class AddConditionDialogComponent {
  form!: FormGroup;
  
  submitted: boolean = false;
  loading: boolean = false;
  validations =  ['Avec Condition', 'Sans Condition'];
  user: any[] = [];
  filteredAssigneOptions$!: Observable<any[]>;
  etat = ['clôturé', 'en cours', 'en attente','suspendu'];
  categorie = ['précondition', 'poste condition'];
  
    constructor(
      private formBuilder: FormBuilder,
      private _utilService: UtilsService,
      protected dialogRef: NbDialogRef<AddConditionDialogComponent>,
      private userService :UserService,
      private wkfService : WkfService
    ) { }
  
    ngOnInit() {
      this.initForm();
  
    
    
    }
  
    initForm(risque?: RisqueInstance) {
      this.form = this.formBuilder.group({
        validation: [''],
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

      createConditionGroup(condition?: Condition): FormGroup {
        console.log("am here")
            return this.formBuilder.group({
              id: [condition ? condition.id : null],
              detail: [condition ? condition.detail : '', [Validators.required]],
              categorie: [condition ? condition.categorie : '', [Validators.required]],
              assigne: [condition ? condition.assigne :'' , [Validators.required]],
              numberSemaineApresLancement: [condition ? condition.numberSemaineApresLancement : '', [Validators.required]],
              dateLancement: [condition ? condition.dateLancement : '', [Validators.required]],
              dateEcheance: [condition ? condition.dateEcheance :'' , [Validators.required]],
              etat: [condition ? condition.etat : '', [Validators.required]],
              commentCond: ['', [Validators.required]],
            });
          }
          get conditions(): FormArray {
            return this.form.get('conditions') as FormArray;
          }
          
        
        
          addCondition(): void {  
            this.conditions.push(this.createConditionGroup());
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
  
    
     
    }
  

}
<nb-card style="width: 700px;">
    <nb-card-header>
      <div class="d-flex justify-content-between">
        <div>
          <span>Validation</span>
        </div>
        <div>
         <button nbButton (click)="closeWindow()"><nb-icon class="modal-icon text-dark" icon="xmark" pack="fas"></nb-icon></button>
        </div>
      </div>
    </nb-card-header>
  
    <nb-card-body>
      <form [formGroup]="form" autocomplete="off">
        <!-- Sponsor Input -->
        <div  class="d-flex justify-content-around my-2 mx-auto p-2">
          <div class="w-25">
            <div class="col-form-label my-auto mx-auto">Type de validation: <span class="text-danger">*</span></div>
          </div>
          <div class="w-75 position-relative">
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
         <div class="d-flex justify-content-around my-2 mx-auto p-2" *ngIf="form.get('validation')?.value =='Sans Condition'">
          <div class="w-25">
            <div class="col-form-label my-auto">Commentaire:<span class="text-danger">*</span></div>
          </div>
          <div class="w-75 position-relative">
            <textarea formControlName="comment" class="form-control" name="comment"
                      placeholder="Veuillez entrer un commentaire" rows="3"></textarea>
            <div *ngIf="submitted && form.get('comment')?.errors?.['required']"
                 class="invalid-feedback d-block position-absolute">
              <small class="text-danger">Le commentaire est obligatoire</small>
            </div>
          </div>
        </div> 


        <!-- Condition form -->
              <div *ngIf="form.get('validation')?.value =='Avec Condition'">
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
                            <nb-option *ngFor="let user of filteredAssigneOptions$ | async" [value]="user.email">
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
                            <input formControlName="numberSemaineApresLancement" nbInput type="number"  fullWidth />
                        </div>
                    </div>
                    <div class="d-flex justify-content-around my-2 mx-auto p-2" *ngIf="conditions.controls[i].get('categorie')?.value === 'poste condition'">
                        <div class="w-50">
                            <label class="col-form-label my-auto">Date  de lancement : <span class="text-danger">*</span></label>
                        </div>
                        <div class="w-50 position-relative">
                          <input formControlName="dateLancement" nbInput placeholder="Sélectionnez une Date"  [nbDatepicker]="dateTimePicker" fullWidth>
                            <nb-datepicker #dateTimePicker></nb-datepicker> 
                        </div>
                    </div>
                    <div class="d-flex justify-content-around my-2 mx-auto p-2" *ngIf="conditions.controls[i].get('categorie')?.value === 'précondition'">
                        <div class="w-50">
                            <label class="col-form-label my-auto">Date échéance : <span class="text-danger">*</span></label>
                        </div>
                        <div class="w-50 position-relative">
                          <input formControlName="dateEcheance" nbInput placeholder="Sélectionnez une Date"  [nbDatepicker]="dateTimePickerEcheance" fullWidth>
                            <nb-datepicker #dateTimePickerEcheance></nb-datepicker> 
                        </div>
                    </div>
                    <div class="d-flex justify-content-around my-2 mx-auto p-2">
                        <div class="w-50">
                            <div class="col-form-label my-auto">Etat   : <span class="text-danger">*</span></div>
                        </div>
                        <div class="w-50 position-relative">
                            <nb-select formControlName="etat" fullWidth placeholder="Sélectionnez etat" autocomplete="off"  name="type" id="type">
                              <nb-option *ngFor="let item of etat"  [value]="item ">{{item}} </nb-option>
                            </nb-select>
                        </div>
                    </div>

                    <div class="d-flex justify-content-around my-2 mx-auto p-2">
                        <div class="w-50">
                          <div class="col-form-label my-auto">Commentaire :</div>
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
  
hey chat when i chose "form.get('validation')?.value =='Avec Condition' the condition form doesnt display it doesnt work
