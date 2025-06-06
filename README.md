@Component({
  selector: 'app-update-condition-dialog',
  templateUrl: './update-condition-dialog.component.html',
})
export class UpdateConditionDialogComponent implements OnInit {
  @Input() data!: ConditionsDTO;
  formGroup!: FormGroup;
  user: any[] = [];
  filteredAssigneOptions$!: Observable<any[]>;
  etat = ['en cours', 'en attente', 'suspendu'];
  categorie = ['précondition', 'poste condition'];

  constructor(
    private _route: ActivatedRoute,
    private wkfService: WkfService,
    private formBuilder: FormBuilder,
    private _utilService: UtilsService,
    private dialogService: NbDialogService,
    private userService: UserService,
    private gestionRisqueService: GestionRisqueService,
    public _authorizationService: AuthorisationService,
    private dialogRef: NbDialogRef<UpdateConditionDialogComponent>
  ) {}

  ngOnInit() {
    this.initFormGroup(this.data);

    this.userService.getUsersList().subscribe(users => {
      this.user = users;
      this.filteredAssigneOptions$ = this.formGroup.get('assigne')!.valueChanges.pipe(
        startWith(''),
        map(filterString => this.filterOptions(filterString, this.user, 'email'))
      );
    });

    // Listen to 'categorie' changes and update validators accordingly
    this.formGroup.get('categorie')?.valueChanges
      .pipe(startWith(this.formGroup.get('categorie')?.value))
      .subscribe(value => {
        this.updateConditionalValidators(value);
      });
  }

  initFormGroup(condition?: ConditionsDTO) {
    this.formGroup = this.formBuilder.group({
      detail: [condition?.detail ?? '', Validators.required],
      categorie: [condition?.categorie ?? '', Validators.required],
      assigne: [condition?.assigne ?? '', Validators.required],
      numberSemaineApresLancement: [condition?.numberSemaineApresLancement ?? ''],
      dateEcheance: [condition?.dateEcheance ? new Date(condition.dateEcheance) : ''],
      etat: [condition?.etat ?? '', Validators.required],
      commentCond: [condition?.commentCondition ?? '']
    });
  }

  updateConditionalValidators(categorieValue: string): void {
    const semaineCtrl = this.formGroup.get('numberSemaineApresLancement');
    const dateCtrl = this.formGroup.get('dateEcheance');

    if (categorieValue === 'précondition') {
      dateCtrl?.setValidators([Validators.required]);
      semaineCtrl?.clearValidators();
      semaineCtrl?.setValue('');
    } else if (categorieValue === 'poste condition') {
      semaineCtrl?.setValidators([Validators.required]);
      dateCtrl?.clearValidators();
      dateCtrl?.setValue('');
    } else {
      dateCtrl?.clearValidators();
      semaineCtrl?.clearValidators();
    }

    dateCtrl?.updateValueAndValidity();
    semaineCtrl?.updateValueAndValidity();
  }

  filterOptions(filterString: string, options: any[], field: string): any[] {
    if (!filterString || typeof filterString !== 'string') return options;
    const filterValue = filterString.toLowerCase();
    return options.filter(option => option[field].toLowerCase().includes(filterValue));
  }

  onReset() {
    this.formGroup.reset();
  }

  onSubmit() {
    if (!this.formGroup.valid) {
      this._utilService.displayWarning("Les champs sont obligatoires", "Attention");
      return;
    }

    console.log("form value: ", this.formGroup.value);

    this.gestionRisqueService.updateCondition(this.formGroup.value, this.data.id).subscribe({
      next: () => {
        this._utilService.displaySucess("Condition mise à jour avec succès", "Succès");
        this.dialogRef.close("SUCCESS");
      },
      error: (err) => {
        this._utilService.displayError(err.error.message || "Erreur lors de la mise à jour", "Erreur");
      },
    });
  }

  closeWindow() {
    this.dialogRef.close();
  }
}
