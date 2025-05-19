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
  
    <nb-card-body style="overflow-x: hidden;">
      <form [formGroup]="form" autocomplete="off">
        <!-- Sponsor Input -->
        <div  class="d-flex justify-content-around my-2 mx-auto p-2" >
          <div class="w-50">
            <div class="col-form-label my-auto mx-auto">Nom de Risque: <span class="text-danger">*</span></div>
          </div>
          <div class="w-50 position-relative">
            <nb-select  fullWidth
                   placeholder="Veuillez sélectionner type" formControlName="name" autocomplete="off"
                   name="validation">
                   
              <nb-option *ngFor="let item of risquesDisponibles" [value]="item?.name">
                {{ item?.name }}
              </nb-option>
              </nb-select>
              
            <div *ngIf="submitted && form.get('name')?.errors?.['required']" class="invalid-feedback d-block position-absolute">
              <small class="text-danger">Veuillez sélectionner type</small>
            </div>
          </div>
        </div>
  
  
      </form>
    </nb-card-body>
  
   
  </nb-card>
  
  <ng-container *ngIf="loading">
    <!-- <rpa-portal-loading></rpa-portal-loading> -->
  </ng-container>
  export class DialogRisqueCordinateurComponent {
    form!: FormGroup;
    @Input() demande!: DemandeQualificationResponse
     risquesDisponibles: Risque[] = [];
    
    submitted: boolean = false;
    loading: boolean = false;
    validations =  ['Avec Condition', 'Sans Condition'];
    user: any[] = [];
    
      constructor(
        private formBuilder: FormBuilder,
        private _utilService: UtilsService,
        protected dialogRef: NbDialogRef<DialogRisqueCordinateurComponent>,
        private userService :UserService,
        private wkfService : WkfService,
        private risqueService: GestionRisqueService
      ) { }
    
     ngOnInit() {
    if (this.demande.id) {
      this.loadRisques();
    }
  }

  loadRisques() {
    this.loading = true;
    this.risqueService.getRisquesDisponibles(this.demande.id).subscribe({
      next: (data) => {
        this.risquesDisponibles = data;
        this.loading = false;
      },
      error: (err) => {
        console.error('Erreur chargement risques', err);
        this.loading = false;
      }
    });
  }
    
      initForm() {
        this.form = this.formBuilder.group({
          name: [''],
        
  
        });
      }
    


    
      closeWindow() {
        this.dialogRef.close();
      }
    


  

}
