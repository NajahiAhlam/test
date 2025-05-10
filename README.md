
<nb-card>
  
    <nb-card-header class="d-flex justify-content-between">
        {{ risques?.name }}
      <button nbButton (click)="closeWindow()">
        <nb-icon class="modal-icon text-dark" icon="xmark" pack="fas"></nb-icon>
      </button>
    </nb-card-header>
  
    <nb-card-body style="overflow-x: hidden;">
        <form [formGroup]="formGroup" autocomplete="off"  style="width: 800px; height: 700px;">
        
  
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
            <div class="d-flex justify-content-around my-2 mx-auto p-2">
                <div class="w-50">
                  <div class="col-form-label my-auto">Avec une condition : <span class="text-danger">*</span></div>
                </div>
                <div class="w-50 position-relative">
                  <!-- Radio Button Group -->
                  <nb-radio-group [(ngModel)]="selectedCondition"  [ngModelOptions]="{standalone: true}">
                    <nb-radio value="oui" (click)="addCondition()" >Oui</nb-radio>
                    <nb-radio value="non">Non</nb-radio>
                  </nb-radio-group>
                </div>
              </div>
              <div *ngIf="selectedCondition === 'non'" class="d-flex justify-content-around my-2 mx-auto p-2">
                <div class="w-50">
                  <div class="col-form-label my-auto" formGroupName="comment">Commentaire :</div>
                </div>
                <div class="w-50 position-relative">
                  <textarea fullWidth class="form-control" placeholder="Ajoutez votre commentaire" rows="3"></textarea>
                </div>
              </div>
                  <div *ngIf="selectedCondition === 'oui'" >
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
                    <div class="d-flex justify-content-around my-2 mx-auto p-2" *ngIf="risque?.initier">
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
    <nb-card-footer class="d-flex flex-row justify-content-end">
        <button type="push" nbButton outline status="danger" class="mx-3" (click)="onReset()">Réinitialiser</button>
        <button type="submit" class="d-flex btn btn-dark mr-3" (click)="onSubmit()">Confirmer</button>
      </nb-card-footer>
  </nb-card>
  import { Component, Input } from '@angular/core';
import { DemandeQualificationResponse } from '../../models/DemandeQualification';
import { WkfService } from '../../services/wkf.service';
import { UtilsService } from 'src/app/@core/UtilsService';
import { UserService } from 'src/app/services/user.service';
import { AnalyseRisque, Condition, Risque, ZoneRisque } from 'src/app/pages/gestion-referentiel/model/risque';
import { NbDialogRef } from '@nebular/theme';
import { AbstractControl, FormArray, FormBuilder, FormControl, FormGroup, Validators } from '@angular/forms';
import { Observable } from 'rxjs';
import { map, startWith, switchMap } from 'rxjs/operators';

@Component({
  selector: 'gems-dialog-risque',
  templateUrl: './dialog-risque.component.html',
  styleUrls: ['./dialog-risque.component.scss']
})
export class DialogRisqueComponent {
  @Input() demande?:DemandeQualificationResponse;
  @Input() demandetId?:number;
  risques!: Risque;
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
    this.wkfService.getRisque().subscribe({
      next:(data:any)=>{
        this.risques = data;
        console.log("risque in ng on init::",this.risques)
        //this._utilService.displaySucess("Commentaire ajouté avec succes","MyCnp")
     
        this.initFormGroup(this.risques)
      }
    })
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
        id: [risque?.id ?? ''], // Add ID field
        contexte: [risque?.contexte??'', Validators.required],
        niveauIntrinseque: [risque?.niveauIntrinseque??'', Validators.required],
        autreRisque: ['', Validators.required],
        niveauRisqueResiduel: [risque?.niveauRisqueResiduel??'', Validators.required],
        comment: [risque?.comment??'', Validators.required],
        conditions: this.formBuilder.array(
          risque?.conditions?.length
            ? risque.conditions.map(c => this.createConditionGroup(c))
            : []
        ),
    
        zoneRisques: this.formBuilder.array(
          risque?.zoneRisques?.length
            ? risque.zoneRisques.map(zone => this.createZoneGroup(zone))
            : []
        )
      });
    }
  
    createZoneGroup(zone: any) {
      return this.formBuilder.group({
        id: [zone.id],
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
        id: [analyseRisque.id],
        description: [analyseRisque.description],
        reponse: [analyseRisque.reponse || ''], // User input for response
        facteur: [analyseRisque.facteur || ''] // User input for facteur
      });
    }
    getFilteredZoneRisques(risque: Risque): ZoneRisque[] {
      const zoneRisques = this.formGroup.get('zoneRisques')?.value || [];
      return zoneRisques.filter((z:any) => z.id && risque.zoneRisques.some(rz => rz.id === z.id));
    }
    
    

    createConditionGroup(condition?: Condition): FormGroup {
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
        return this.formGroup.get('conditions') as FormArray;
      }
      
    
    
      addCondition(): void {  
        this.conditions.push(this.createConditionGroup());
      }
   
      
    
      removeConditions(index: number): void {
        this.conditions.removeAt(index);
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
  onReset(){
    this.formGroup.reset()
    this.conditions.clear()
  }

}
