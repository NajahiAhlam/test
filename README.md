export class UpdateConditionDialogComponent {
  @Input() data!: ConditionsDTO
  formGroup!: FormGroup
  user: any[] = [];
  filteredAssigneOptions$!: Observable<any[]>;
  etat = ['en cours', 'en attente','suspendu'];
  categorie = ['précondition', 'poste condition'];

  constructor(private _route:ActivatedRoute,private wkfService:WkfService,
    private formBuilder: FormBuilder,
      private _utilService:UtilsService,private dialogService: NbDialogService,
      private userService: UserService,
      private gestionRisqueService: GestionRisqueService,
      public _authorizationService:AuthorisationService, private dialogRef: NbDialogRef<UpdateConditionDialogComponent>) { }

  ngOnInit() {
    this.initFormGroup(this.data)
      this.userService.getUsersList().subscribe(users => {
    this.user = users;
  });
  
     this.userService.getUsersList().subscribe(users => {
      this.user = users;
      this.filteredAssigneOptions$ = this.formGroup.get('assigne')!.valueChanges.pipe(
        startWith(''),
        map(filterString => this.filterOptions(filterString, this.user, 'email'))
      );
    });
  }

  initFormGroup(condition ?: ConditionsDTO) {
    this.formGroup = this.formBuilder.group({
      detail:[condition?.detail??'', Validators.required],
      categorie:[condition?.categorie??'', Validators.required],
      assigne:[condition?.assigne??'', Validators.required],
      numberSemaineApresLancement: [condition?.numberSemaineApresLancement??'', Validators.required],
   //   dateLancement:[condition?.dateLancement ? new Date(condition.dateLancement) : '', Validators.required],
      dateEcheance:[condition?.dateEcheance ? new Date(condition.dateEcheance) :'', Validators.required],
      etat:[condition?.etat??'', Validators.required],
      commentCond: [condition?.commentCondition??'']
    })

  }
    filterOptions(filterString: string, options: any[], field: string): any[] {
  if (!filterString || typeof filterString !== 'string') {
    return options;
  }
  const filterValue = filterString.toLowerCase();
  return options.filter(option => option[field].toLowerCase().includes(filterValue));
}
  onReset() {
    this.formGroup.reset()
  }
  onSubmit(){
    if (!this.formGroup.valid) {
      this._utilService.displayWarning("Les champs sont obligatoires", "Attention");
      return;
    }
    if(this.formGroup.get('categorie')?.value === 'précondition') {
         this.formGroup.get('dateLancement')?.setValue('');
         this.formGroup.get('numberSemaineApresLancement')?.setValue('');
    }
    else {
        this.formGroup.get('dateEcheance')?.setValue('');
    }

    console.log("form value: ",this.formGroup.value)
     this.gestionRisqueService.updateCondition(this.formGroup.value, this.data.id).subscribe({
      next: () => {
        this._utilService.displaySucess("Condition mis a jour avec succès", "Succès");
        this.dialogRef.close("SUCCESS");
      },
      error: (err) => {
        this._utilService.displayError(err.error.message, "Erreur");
      },
    });
  }
   closeWindow() {
    this.dialogRef.close();
  }

}
