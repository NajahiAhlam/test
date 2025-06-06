
<nb-card style="width: 700px;">
  
    <nb-card-header class="d-flex justify-content-between">
        {{ risques?.name }}
      <button nbButton (click)="closeWindow()">
        <nb-icon class="modal-icon text-dark" icon="xmark" pack="fas"></nb-icon>
      </button>
    </nb-card-header>
  
    <nb-card-body style="overflow-x: hidden;">
        <form [formGroup]="formGroup" autocomplete="off"  style="height: 600px;">
        
  
        <div class="d-flex justify-content-around my-2 mx-auto p-2">
            <div class="w-50">
                <div class="col-form-label my-auto">Contexte et principes structurants du nouveau produit : <span class="text-danger">*</span></div>
            </div>
            <div class="w-50 position-relative">
                <textarea fullWidth class="form-control" formControlName="contexte"  placeholder="Saisissez Contexte"  rows="3"></textarea>
            </div>
        </div>
        <div class="d-flex justify-content-around my-2 mx-auto p-2">
            <div class="w-50">
                <div class="col-form-label my-auto">Niveau de risque intrinsèque : <span class="text-danger">*</span></div>
            </div>
            <div class="w-50 position-relative">
                <nb-select formControlName="niveauIntrinseque" fullWidth placeholder="Sélectionnez Niveau de risque intrinsèque" autocomplete="off"  name="type" id="type">
                  <nb-option *ngFor="let item of niveau" [value]="item">{{item}}</nb-option>
                </nb-select>
            </div>
        </div>
        <div class='hr'>
            <span class='hr-title'>Zones de risques</span>
          </div>
          <nb-accordion>
            <nb-accordion-item *ngFor="let zone of zoneRisquesArray.controls; let i = index">
              <nb-accordion-item-header>{{ zone.get('name')?.value }}</nb-accordion-item-header>
              <nb-accordion-item-body>
                <div *ngFor="let analyseRisque of getAnalyseRisquesArray(zone).controls; let j = index">

                  <label class="mt-2">{{ analyseRisque.get('description')?.value }}</label>
                  <textarea [formControl]="getFormControl(analyseRisque.get('reponse'))" fullWidth class="form-control mt-2" placeholder="Ajoutez une réponse"></textarea>
                  <div class="d-flex justify-content-around my-2 mx-auto p-2">
                    <div class="w-100 position-relative">
                      <nb-select fullWidth placeholder="Sélectionnez Facteurs d’atténuation" autocomplete="off" name="type" 
                      [formControl]="getFormControl(analyseRisque.get('facteur'))" id="type">
                      <nb-option *ngFor="let item of categorie" [value]="item">{{ item }}</nb-option>
                    </nb-select>
                    </div>
                  </div>
                </div>
          
              </nb-accordion-item-body>
            </nb-accordion-item>
          </nb-accordion>
          

          
        <div class='hr'>
            <span class='hr-title'></span>
          </div>
          <div class="d-flex justify-content-around my-2 mx-auto p-2">
            <div class="w-50">
                <div class="col-form-label my-auto">Autre Risque :</div>
                </div>
                <div class="w-50 position-relative">
                    <textarea fullWidth class="form-control" formControlName="autreRisque" name="descriptionProduit" id="descriptionProduit" placeholder="Saisissez Analyse"  rows="3"></textarea>
                </div>
            </div>
            <div class="d-flex justify-content-around my-2 mx-auto p-2">
                <div class="w-50">
                    <div class="col-form-label my-auto">Niveau de risque résiduel : <span class="text-danger">*</span></div>
                </div>
                <div class="w-50 position-relative">
                    <nb-select formControlName="niveauRisqueResiduel" fullWidth placeholder="Sélectionnez Niveau de risque résiduele" autocomplete="off"  name="type" id="type">
                      <nb-option *ngFor="let item of niveau" [value]="item">{{item}}</nb-option>
                    </nb-select>
                </div>
            </div>
         
  
    </form>
    </nb-card-body>
    <nb-card-footer class="d-flex flex-row justify-content-end">
        <button type="push" nbButton outline status="danger" class="mx-3" (click)="onReset()">Réinitialiser</button>
        <button type="submit" class="d-flex btn btn-dark mr-3" (click)="onSubmit()">Confirmer</button>
      </nb-card-footer>
  </nb-card>
  export class DialogRisqueComponent {
  @Input() demande?:DemandeQualificationResponse;
  @Input() demandetId?:number;
  @Input() risques!: Risque;
  @Input() risque!: Risque;
  selectedCondition?: string; 
  formGroup!: FormGroup
  user: any[] = [];
  filteredAssigneOptions$!: Observable<any[]>;
  etat = ['clôturé', 'en cours', 'en attente','suspendu'];
  categorie = ['précondition', 'poste condition'];
  niveau = ['Faible  ', 'Moyene', 'Forte'];



  constructor(private wkfService: WkfService,
    private _utilService:UtilsService,
        private formBuilder: FormBuilder,
    private userService: UserService,
    private dialogRef: NbDialogRef<DialogRisqueComponent>) { }

  ngOnInit(): void {
    if(!this.risques!) {
   this.wkfService.getRisque().subscribe({
      next:(data:any)=>{
        this.risques = data;
        console.log("risque in ng on init::",this.risques)
        //this._utilService.displaySucess("Commentaire ajouté avec succes","MyCnp")
     
        this.initFormGroup(this.risques)
      }
    })
    }
 
 this.initFormGroup(this.risques)
    this.userService.getUsersList().subscribe(users => {
      this.user = users.filter(el => el.enabled === true);
      const conditionGroup = this.createConditionGroup();
      if (conditionGroup && conditionGroup.get('assigne')) {
        this.filteredAssigneOptions$ = conditionGroup.get('assigne')!.valueChanges.pipe(
          startWith(''),
          map(filterString => this.filterOptions(filterString, this.user, 'email'))
        );
        console.log(" this.filteredAssigneOptions$: ", this.filteredAssigneOptions$)
      }
    });
    this.userService.getUsersList().subscribe(users => {
      console.log("Users:", users);

      this.user = users;
      console.log("Filtered Users:", this.user);
    });
    
    

   
  }
  filterOptions(filterString: string, options: any[], field: string): any[] {
    const filterValue = filterString.toLowerCase();
    return options.filter(option => option[field].toLowerCase().includes(filterValue));
  }
 
    initFormGroup(risque?: Risque) {
      this.formGroup = this.formBuilder.group({
        risqueId: [risque?.id ?? ''], // Add ID field
        contexte: [risque?.contexte??'', Validators.required],
        niveauIntrinseque: [risque?.niveauIntrinseque??'', Validators.required],
        autreRisque: [''],
        niveauRisqueResiduel: [risque?.niveauRisqueResiduel??'', Validators.required],
        comment: [risque?.comment??'', Validators.required],
        zoneRisques: this.formBuilder.array(
          risque?.zoneRisques?.length
            ? risque.zoneRisques.map(zone => this.createZoneGroup(zone))
            : []
        )
      });
    }
  
    createZoneGroup(zone: any) {
      return this.formBuilder.group({
        zoneRisqueId: [zone.id],
        name: [zone.name],
        analyseRisques: this.formBuilder.array(
          zone.analyseRisques?.length
            ? zone.analyseRisques.map((a:any) => this.createAnalyseRisqueGroup(a))
            : []
        )
      });
    }
    get zoneRisquesArray(): FormArray {
      return (this.formGroup?.get('zoneRisques') as FormArray) ?? this.formBuilder.array([]);
    }
    
    getFormControl(control: any): FormControl {
      return control as FormControl;
    }
    getAnalyseRisquesArray(zone: AbstractControl): FormArray {
      return zone.get('analyseRisques') as FormArray;
    }
    
    createAnalyseRisqueGroup(analyseRisque: AnalyseRisque) {
      return this.formBuilder.group({
        analyseRisqueId: [analyseRisque.id],
        description: [analyseRisque.description || ''],
        reponse: [analyseRisque.reponse || ''],
        facteur: [analyseRisque.facteur || ''] 
      });
    }
    getFilteredZoneRisques(risque: Risque): ZoneRisque[] {
      const zoneRisques = this.formGroup.get('zoneRisques')?.value || [];
      return zoneRisques.filter((z:any) => z.id && risque.zoneRisques.some(rz => rz.id === z.id));
    }
    
    
  closeWindow() {
    this.dialogRef.close();
  }
  onSubmit(){
   /* if (!this.formGroup.valid) {
      this._utilService.displayWarning("Les champs sont obligatoires", "Attention");
      return;
    }*/

    console.log("form value: ",this.formGroup.value)
     this.wkfService.updateRisque(this.demandetId!,this.formGroup.value).subscribe({
      next: () => {
        this._utilService.displaySucess("Risque ajouter avec succès", "Succès");
        this.dialogRef.close("SUCCESS");
      },
      error: (err) => {
        this._utilService.displayError(err.error.message, "Erreur");
      },
    });
  }
  onReset() {
      this.formGroup.get('niveauRisqueResiduel')?.reset('');
      this.formGroup.get('autreRisque')?.reset('');
      this.formGroup.get('niveauIntrinseque')?.reset('');
      this.formGroup.get('contexte')?.reset('');
      this.zoneRisquesArray.controls.forEach(zoneGroup => {
        const analyseInstances = zoneGroup.get('analyseRisques') as FormArray;

        analyseInstances.controls.forEach(analyseGroup => {
          analyseGroup.get('reponse')?.reset('');
          analyseGroup.get('facteur')?.reset('');
        });
      });
}

}
