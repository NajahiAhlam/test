
<nb-card style="width: 700px;">
  
    <nb-card-header class="d-flex justify-content-between">
      Modifier {{ risques?.risque?.name }}
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
        <nb-accordion-item *ngFor="let zone of zoneRisquesArray.controls">
          <nb-accordion-item-header>{{ zone.get('name')?.value }}</nb-accordion-item-header>
          <nb-accordion-item-body>
            <div *ngFor="let analyseRisque of getAnalyseRisquesArray(zone).controls">
               <label class="mt-2">{{ analyseRisque.get('description')?.value }}</label> 

              <textarea [formControl]="getFormControl(analyseRisque.get('reponse')?.value)" fullWidth class="form-control mt-2" placeholder="Ajoutez une réponse"></textarea>
              
              <div class="d-flex justify-content-around my-2 mx-auto p-2">
                <div class="w-100 position-relative">
                  <nb-select [formControl]="getFormControl(analyseRisque.get('facteur'))" fullWidth placeholder="Sélectionnez Facteurs d’atténuation">
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
                <div class="col-form-label my-auto">Autre Risque : <span class="text-danger">*</span></div>
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
  export class DialogUpdateRisqueComponent {
    @Input() risques!: RisqueInstanceDTO;
    @Input() risqueIntanceId?:number;
    formGroup!: FormGroup
    categorie = ['précondition', 'poste condition'];
    niveau = ['Faible  ', 'Moyene', 'Forte'];
  
  
  
    constructor(private wkfService: WkfService,
      private _utilService:UtilsService,
          private formBuilder: FormBuilder,
      private userService: UserService,
      private dialogRef: NbDialogRef<DialogUpdateRisqueComponent>) { }
  
    ngOnInit(): void {
   
   
   this.initFormGroup(this.risques)
     
      
  
     
    }
    filterOptions(filterString: string, options: any[], field: string): any[] {
      const filterValue = filterString.toLowerCase();
      return options.filter(option => option[field].toLowerCase().includes(filterValue));
    }
   
    initFormGroup(risque?: RisqueInstanceDTO) {
      this.formGroup = this.formBuilder.group({
        id: [risque?.id ?? ''], 
       // risqueId: [risque?.risque?.id],
        contexte: [risque?.contexte ?? '', Validators.required],
        niveauIntrinseque: [risque?.niveauIntrinseque ?? '', Validators.required],
        niveauRisqueResiduel: [risque?.niveauRisqueResiduel ?? '', Validators.required],
        autreRisque: [risque?.autreRisque ?? ''],
        risque: this.formBuilder.group({
          id: [risque?.risque?.id ?? ''],
          name: [risque?.risque?.name ?? ''],
          validateurEmail: [risque?.risque?.validateurEmail ?? '']
        }),
        zoneRisques: this.formBuilder.array(
          risque?.zoneRisques?.length
            ? risque.zoneRisques.map(zone => this.createZoneGroup(zone))
            : []
        )
      });
    }

    createZoneGroup(zone: any) {
      return this.formBuilder.group({
        id: [zone.id ?? ''],
        name: [zone.zoneRisque?.name ?? ''],
        zoneRisqueId: [zone.zoneRisque?.id ?? ''],
        analyseInstances: this.formBuilder.array(
          zone.analyseInstances?.length
            ? zone.analyseInstances.map((instance: any) => this.createAnalyseRisqueGroup(instance))
            : []
        )
      });
      
    }

    createAnalyseRisqueGroup(instance: any) {
      return this.formBuilder.group({
        id: [instance.id ?? ''],
        reponse: [instance.reponse ?? ''],
        facteur: [instance.facteur ?? ''],
        analyseRisque: this.formBuilder.group({
          id: [instance.analyseRisque?.id ?? ''],
        description: [instance.analyseRisque?.description ?? '']
      

        })
      });
    }




    get zoneRisquesArray(): FormArray {
      return (this.formGroup?.get('zoneRisques') as FormArray) ?? this.formBuilder.array([]);
    }

    getAnalyseRisquesArray(zone: AbstractControl): FormArray {
      return zone.get('analyseInstances') as FormArray;
    }
    getFormControl(control: AbstractControl | null): FormControl {
      return (control as FormControl) ?? new FormControl('');
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
     this.wkfService.updateRisqueInstance(this.risqueIntanceId!,this.formGroup.value).subscribe({
        next: () => {
          this._utilService.displaySucess("Risque mis a jour avec succès", "Succès");
          this.dialogRef.close("SUCCESS");
        },
        error: (err) => {
          this._utilService.displayError(err.error.message, "Erreur");
        },
      });
    }
    onReset(){
      this.formGroup.reset()
    }

}
hey i have a probleme is the  <label class="mt-2">{{ analyseRisque.get('description')?.value }}</label>  doesnt show
