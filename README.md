<nb-card style="width: 700px;">
  <nb-card-header>
    <div class="d-flex justify-content-between">
      <div><span>Grille de Validation</span></div>
      <div>
        <button nbButton (click)="closeWindow()">
          <nb-icon class="modal-icon text-dark" icon="xmark" pack="fas"></nb-icon>
        </button>
      </div>
    </div>
  </nb-card-header>

  <nb-card-body style="overflow-x: hidden;">
    <form [formGroup]="form" autocomplete="off">
      <!-- Type de Validation -->
      <div class="d-flex justify-content-around my-2 mx-auto p-2">
        <div class="w-50">
          <div class="col-form-label my-auto mx-auto">Type de validation: <span class="text-danger">*</span></div>
        </div>
        <div class="w-50 position-relative">
          <nb-select fullWidth placeholder="Veuillez sélectionner type" formControlName="validation">
            <nb-option *ngFor="let val of validations" [value]="val">{{ val }}</nb-option>
          </nb-select>
          <div *ngIf="submitted && form.get('validation')?.errors?.['required']" class="invalid-feedback d-block position-absolute">
            <small class="text-danger">Veuillez sélectionner type</small>
          </div>
        </div>
      </div>

      <!-- Commentaire (Sans Condition) -->
      <div *ngIf="validationValue === 'Sans Condition'" class="d-flex justify-content-around my-2 mx-auto p-2">
        <div class="w-50">
          <div class="col-form-label my-auto">Commentaire: <span class="text-danger">*</span></div>
        </div>
        <div class="w-50 position-relative">
          <textarea formControlName="comment" class="form-control" placeholder="Veuillez entrer un commentaire" rows="3"></textarea>
          <div *ngIf="submitted && form.get('comment')?.errors?.['required']" class="invalid-feedback d-block position-absolute">
            <small class="text-danger">Le commentaire est obligatoire</small>
          </div>
        </div>
      </div>

      <!-- Conditions (Avec Condition) -->
      <div *ngIf="validationValue === 'Avec Condition'" style="height: 500px;">
        <div formArrayName="conditions">
          <div *ngFor="let condition of conditions.controls; let i = index" [formGroupName]="i" class="border p-2 mb-2">
            <!-- Detail -->
            <div class="d-flex justify-content-around my-2 mx-auto">
              <div class="w-50"><label class="col-form-label">Détail condition {{ i + 1 }}:</label></div>
              <div class="w-50"><textarea formControlName="detail" class="form-control" rows="3" placeholder="Ajoutez votre détail"></textarea></div>
            </div>

            <!-- Catégorie -->
            <div class="d-flex justify-content-around my-2 mx-auto">
              <div class="w-50"><label class="col-form-label">Catégorie: <span class="text-danger">*</span></label></div>
              <div class="w-50">
                <nb-select formControlName="categorie" fullWidth placeholder="Sélectionnez une catégorie">
                  <nb-option *ngFor="let item of categorie" [value]="item">{{ item }}</nb-option>
                </nb-select>
              </div>
            </div>

            <!-- Assigné -->
            <div class="d-flex justify-content-around my-2 mx-auto">
              <div class="w-50"><label class="col-form-label">Assigné: <span class="text-danger">*</span></label></div>
              <div class="w-50">
                <input [nbAutocomplete]="autoAssigne" nbInput fullWidth placeholder="Veuillez sélectionner un utilisateur"
                  formControlName="assigne" />
                <nb-autocomplete #autoAssigne>
                  <nb-option *ngFor="let user of filteredAssigneOptions[i] | async" [value]="user.email">
                    {{ user.email }}
                  </nb-option>
                </nb-autocomplete>
              </div>
            </div>

            <!-- Champs conditionnels -->
            <div *ngIf="condition.get('categorie')?.value === 'poste condition'" class="d-flex justify-content-around my-2 mx-auto">
              <div class="w-50"><label class="col-form-label">Nombre de semaines après lancement:</label></div>
              <div class="w-50"><input nbInput type="number" formControlName="numberSemaineApresLancement" fullWidth /></div>
            </div>

            <div *ngIf="condition.get('categorie')?.value === 'précondition'" class="d-flex justify-content-around my-2 mx-auto">
              <div class="w-50"><label class="col-form-label">Date d'échéance:</label></div>
              <div class="w-50">
                <input nbInput formControlName="dateEcheance" [nbDatepicker]="dp" placeholder="Sélectionnez une date" fullWidth />
                <nb-datepicker #dp></nb-datepicker>
              </div>
            </div>

            <!-- Commentaire assigné -->
            <div class="d-flex justify-content-around my-2 mx-auto">
              <div class="w-50"><label class="col-form-label">Commentaire de l'assigné:<span class="text-danger">*</span></label></div>
              <div class="w-50"><textarea class="form-control" formControlName="commentCond" rows="3"></textarea></div>
            </div>

            <!-- Bouton supprimer -->
            <div class="text-right">
              <button type="button" class="btn btn-danger" (click)="removeConditions(i)">Supprimer</button>
            </div>
          </div>
        </div>

        <!-- Bouton ajouter -->
        <div class="text-center">
          <button type="button" class="btn btn-secondary" (click)="addCondition()">Ajouter une condition</button>
        </div>
      </div>
    </form>
  </nb-card-body>

  <nb-card-footer>
    <div class="d-flex justify-content-end">
      <button type="button" nbButton outline status="danger" class="mx-3" (click)="onResetForm()">Réinitialiser</button>
      <button type="submit" class="btn btn-dark" (click)="onSubmit()">Valider</button>
    </div>
  </nb-card-footer>
</nb-card>
@Component({
  selector: 'app-add-condition-dialog',
  templateUrl: './add-condition-dialog.component.html',
})
export class AddConditionDialogComponent implements OnInit {
  @Input() risqueId!: number;

  form!: FormGroup;
  submitted = false;
  loading = false;
  validations = ['Avec Condition', 'Sans Condition'];
  categorie = ['précondition', 'poste condition'];
  user: any[] = [];
  validationValue = '';
  filteredAssigneOptions: BehaviorSubject<any[]>[] = [];

  constructor(
    private fb: FormBuilder,
    private _utilService: UtilsService,
    private dialogRef: NbDialogRef<AddConditionDialogComponent>,
    private userService: UserService,
    private risqueInstanceService: GestionRisqueService
  ) {}

  ngOnInit(): void {
    this.initForm();
    this.userService.getUsersList().subscribe(users => {
      this.user = users;
    });

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
  }

  initForm(): void {
    this.form = this.fb.group({
      validation: ['', Validators.required],
      comment: [''],
      conditions: this.fb.array([])
    });
  }

  get conditions(): FormArray {
    return this.form.get('conditions') as FormArray;
  }

  createConditionGroup(condition?: any): FormGroup {
    const group = this.fb.group({
      id: [condition?.id ?? null],
      detail: [condition?.detail ?? '', Validators.required],
      categorie: [condition?.categorie ?? '', Validators.required],
      assigne: [condition?.assigne ?? '', Validators.required],
      numberSemaineApresLancement: [condition?.numberSemaineApresLancement ?? ''],
      dateEcheance: [condition?.dateEcheance ?? ''],
      commentCond: [condition?.commentCond ?? '', Validators.required],
    });

    // Setup conditional validators
    group.get('categorie')?.valueChanges
      .pipe(startWith(group.get('categorie')?.value))
      .subscribe(value => {
        const dateCtrl = group.get('dateEcheance');
        const semaineCtrl = group.get('numberSemaineApresLancement');

        if (value === 'précondition') {
          dateCtrl?.setValidators([Validators.required]);
          semaineCtrl?.clearValidators();
        } else if (value === 'poste condition') {
          semaineCtrl?.setValidators([Validators.required]);
          dateCtrl?.clearValidators();
        } else {
          dateCtrl?.clearValidators();
          semaineCtrl?.clearValidators();
        }

        dateCtrl?.updateValueAndValidity({ onlySelf: true });
        semaineCtrl?.updateValueAndValidity({ onlySelf: true });
      });

    return group;
  }

  addCondition(): void {
    const group = this.createConditionGroup();
    this.conditions.push(group);

    const index = this.conditions.length - 1;
    const assigneFilter$ = new BehaviorSubject<any[]>(this.user);
    this.filteredAssigneOptions[index] = assigneFilter$;

    group.get('assigne')?.valueChanges
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
    const value = filterString.toLowerCase();
    return options.filter(opt => opt[field].toLowerCase().includes(value));
  }

  closeWindow(): void {
    this.dialogRef.close();
  }

  onResetForm(): void {
    this.form.reset();
  }

  onSubmit(): void {
    this.submitted = true;
    this.loading = true;

    if (this.form.invalid) {
      this._utilService.displayWarning("Veuillez remplir tous les champs", "MYCNP");
      this.loading = false;
      return;
    }

    const payload: any = {
      typeValidation: this.form.value.validation,
      comment: this.form.value.comment,
      conditions: this.form.value.conditions || [],
    };

    this.risqueInstanceService.validerRisque(this.risqueId, payload).subscribe({
      next: () => {
        this._utilService.displaySucess("Votre validation a été ajoutée avec succès", "MYCNP");
        this.dialogRef.close("SUCCESS");
      },
      error: (err) => {
        this._utilService.displayError(err.errorMessage || "Erreur lors de la validation", "Erreur");
        this.loading = false;
      }
    });
  }
}
