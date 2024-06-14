https://themesdesign.in/tailwick/html/dashboards-analytics.html


<nb-tabset>
  <nb-tab tabTitle="Données Fournisseur" badgeDot badgeStatus="warning" >
    <form autocomplete="off" [formGroup]="formGroup">
      <div class="d-flex justify-content-around my-2 mx-auto p-2">
      </div>
      <div class="d-flex justify-content-around my-2 mx-auto p-2">
        <div class="w-50">
          <label class="col-form-label my-auto">Fournisseur: <span class="text-danger">*</span> </label>
        </div>
        <div class="w-50 ">
          <nb-select
            fullWidth
            id="fournisseur"
            placeholder="Sélectionnez le fournisseur approprié"
            formControlName="fournisseur"
            selectrequired
          >
            <nb-option *ngFor="let param of fournisseurs | async" [value]="param.id">
              {{param.nom}}
            </nb-option>
          </nb-select>
      
        </div>
      </div>
      <div class="d-flex justify-content-around my-2 mx-auto p-2">
        <div class="w-50">
          <label class="col-form-label my-auto">commendataire: <span class="text-danger">*</span> </label>
        </div>
        <div class="w-50 ">
          <nb-select
            fullWidth
            id="user"
            formControlName="user"
            placeholder="Sélectionnez le Commanditaire approprié"
            selectrequired
          >
            <nb-option *ngFor="let param of users | async" [value]="param.id">
              {{param.firstName}}
            </nb-option>
          </nb-select>
        
        </div>
      </div>
      <div class="d-flex justify-content-around my-2 mx-auto p-2">
        <div class="w-50">
          <label class="col-form-label my-auto">Correspondant:</label>
        </div>
        <div class="w-50">
          <input
              fullWidth
              class=" form-control text-uppercase"
              nbInput
              id="correspondant"
              formControlName="correspondant"
          />
        </div>
      </div>
      <div class="d-flex justify-content-around my-2 mx-auto p-2">
        <div class="w-50">
          <label class="col-form-label my-auto">Date Validite KYS:  </label>
        </div>
        <div class="w-50">
          <input nbInput class="form-control" type="date"
          id="dateValidateKYS"
          autocomplete="off"
          formControlName="dateValidateKYS"
          >
        </div>
      </div>

    </form>

  </nb-tab>
  
  <nb-tab tabTitle="Données Contrat" badgeDot badgeStatus="warning">
    <form [formGroup]="formGroup1">
<div class="d-flex justify-content-around my-2 mx-auto p-2">
    </div>
    <div class="d-flex justify-content-around my-2 mx-auto p-2">
      <div class="w-50">
        <label class="col-form-label my-auto">Nature Contrat: <span class="text-danger">*</span> </label>
      </div>
      <div class="w-50">
        <nb-select
          fullWidth
          id="natureContrat"
          placeholder="Sélectionnez  nature Contrat approprié"
          selectrequired
          formControlName="natureContrat"
        >
          <nb-option *ngFor="let param of natureContrat" [value]="param">
            {{param}}
          </nb-option>
        </nb-select>
      </div>
    </div>
    <div class="d-flex justify-content-around my-2 mx-auto p-2">
      <div class="w-50">
        <label class="col-form-label my-auto">Réf Contrat: </label>
      </div>
      <div class="w-50">
        <input nbInput class="form-control"
        id="refContrat"
        autocomplete="off"
        formControlName="refContrat"
        >
      </div>
    </div>
   
    


    <div class="d-flex justify-content-around my-2 mx-auto p-2">
      <div class="w-50">
        <label class="col-form-label my-auto" >Type Contrat: </label>
      </div>
      <div class="w-50">
        <nb-select
          fullWidth
          id="type"
          placeholder="Sélectionnez le type approprié"
          formControlName="type"
        >
          <nb-option *ngFor="let param of type" [value]="param">
            {{param}}
          </nb-option>
        </nb-select>
      </div>
    </div>

    <div class="d-flex justify-content-around my-2 mx-auto p-2" *ngIf="formGroup1.get('type')?.value ==='Avenant'">
      <div class="w-50">
        <label class="col-form-label my-auto">Contrat initial: <span class="text-danger">*</span> </label>
      </div>
      <div class="w-50">
        <nb-select
          fullWidth
          id="natureContrat"
          placeholder="Sélectionnez  nature Contrat approprié"
          selectrequired
          formControlName="natureContrat"
        >
          <nb-option *ngFor="let param of contratInitial" [value]="param">
            {{param}}
          </nb-option>
        </nb-select>
      </div>
    </div>
    
    <div class="d-flex justify-content-around my-2 mx-auto p-2">
      <div class="w-50">
        <label class="col-form-label my-auto">Lead Contrat:</label>
      </div>
      <div class="w-50 ">
        <nb-select
          fullWidth
          id="leadContrat"
          placeholder="Sélectionnez le lead approprié"
          formControlName="leadContrat"
         
        >
          <nb-option *ngFor="let param of leadContrat" [value]="param" >
            {{param}}
          </nb-option>
        </nb-select>
      </div>
    </div>
    <div class="d-flex justify-content-around my-2 mx-auto p-2">
      <div class="w-50">
        <label class="col-form-label my-auto" >Status Contrat:</label>
      </div>
      <div class="w-50 ">
        <nb-select
          fullWidth
          id="status"
          placeholder="Sélectionnez le status approprié"
          formControlName="status"
        >
          <nb-option *ngFor="let param of status" [value]="param" >
            {{param}}
          </nb-option>
        </nb-select>
      </div>
    </div>
    </form>
    
 
  </nb-tab>
  <nb-tab tabTitle="Détails Contrat" badgeDot badgeStatus="success">
    <form [formGroup]="formGroup2">
      <div class="d-flex justify-content-around my-2 mx-auto p-2">
        <div class="w-50">
          <label class="col-form-label my-auto">Date signature: </label>
        </div>
        <div class="w-50">
          <input nbInput class="form-control" type="date"
          id="dateSignatureContrat"
          autocomplete="off"
          formControlName="dateSignatureContrat"
          >
        </div>
      </div>
      <div class="d-flex justify-content-around my-2 mx-auto p-2">
        <div class="w-50">
          <label class="col-form-label my-auto" >Date validité: </label>
        </div>
        <div class="w-50">
          <input nbInput type="date" class="form-control"
          id="dateValiditeContrat"
          autocomplete="off"
          formControlName="dateValiditeContrat"
          >
        </div>
      </div>
      <div class="d-flex justify-content-around my-2 mx-auto p-2">
        <div class="w-50">
          <label class="col-form-label my-auto">Délais Paiement: </label>
        </div>
        <div class="w-50">
          <input nbInput class="form-control"
          id="delaisPaiement"
          autocomplete="off"
          formControlName="delaisPaiement"
          >
        </div>
      </div>
      <div class="d-flex justify-content-around my-2 mx-auto p-2">
        <div class="w-50">
          <label class="col-form-label my-auto" >Montant HT: </label>
        </div>
        <div class="w-50">
          <input nbInput class="form-control"
          id="montantHt"
          autocomplete="off"
          formControlName="montantHt"
          >
        </div>
      </div>
      <div class="d-flex justify-content-around my-2 mx-auto p-2">
        <div class="w-50">
          <label class="col-form-label my-auto" >devise: </label>
        </div>
        <div class="w-50 ">
          <nb-select
            fullWidth
            id="devise"
            placeholder="Sélectionnez devise approprié"
            selectrequired
            formControlName="devise"
          >
            <nb-option *ngFor="let param of devise" [value]="param">
             {{param}}
            </nb-option>
          </nb-select>
        </div>
      </div>
      <div class="d-flex justify-content-around my-2 mx-auto p-2">
        <div class="w-50">
          <label class="col-form-label my-auto">Contrat Scanné:  </label>
        </div>
        <div class="w-50">
          <input id="file" nbInput type="file" class="form-control"
          autocomplete="off"
          formControlName="file"
          />
        </div>
      </div>
    </form> 
  </nb-tab>
 
</nb-tabset>

<div class="form-row d-flex justify-content-end">
  <div class="form-group px-2 d-flex">
    <button type="push" style="color: #ff3d71; background-color: #ff3d7141; border-color: #ff3d71;" nbButton outline status="danger" class="mx-3"
            (click)="onReset()">
      Réinitialiser
    </button>

    <button type="submit" class="d-flex btn btn-dark mr-3">
      <div (click)="onSave()" class="editUser">Confirmer</div>
    </button>
 </div> 
</div>
----------------------------------------------------------------------------
import {Component, Input, OnInit} from '@angular/core';
import {FormBuilder, FormGroup, Validators} from "@angular/forms";
import { UtilsService } from 'src/app/@core/services/utils.service';
import {NbToastrService, NbWindowRef} from "@nebular/theme";
import { UserApiService } from 'src/app/@core/services/user-api.service';
import {Observable, tap} from "rxjs";
import { GestionContratService } from '../../services/gestion-contrat.service';
import { Contrat, ContratInitial, Devise, Lead, NatureContrat, Status, Type } from 'src/app/@core/models/contrat.model';
import { Fournisseur } from 'src/app/@core/models/fournisseur.mode';
import { GestionFournisseurService } from 'src/app/pages/gestion-referentiel/services/gestion-fournisseur.service';
import { User } from 'src/app/@core/models/user.model';
import { GestionUtilisateurService } from 'src/app/pages/gestion-utilisateur/service/gestion-utilisateur.service';
@Component({
  selector: 'app-dialog-contrat',
  templateUrl: './dialog-contrat.component.html',
  styleUrls: ['./dialog-contrat.component.scss']
})
export class DialogContratComponent {

  fournisseurs?: Observable<Fournisseur[]> | undefined;
  users?: Observable<User[]> | undefined;
  status = Object.values(Status);
  natureContrat = Object.values(NatureContrat);
  type = Object.values(Type);
  leadContrat = Object.values(Lead);
  contratInitial = Object.values(ContratInitial);
  devise = Object.values(Devise);
  fournisseurs$?: Observable<Fournisseur> | undefined;
  @Input()
  id?: number;
  

  @Input() contrat: Contrat | undefined;
 // @Input() roles: CockpitRole[] | undefined;

// @Input fournisseurs: Fournisseur

  mode?: 'UPDATE' | 'CREATE' | 'READ';

  formGroup!: FormGroup;
  formGroup1!: FormGroup;
  formGroup2!: FormGroup;

  submitted = false;
 // roles$: Observable<CockpitRole[]> | undefined;
  selectedItems = [];

  constructor(
    private toastrService: NbToastrService,
    private utilsService: UtilsService,
    private gestionContratService : GestionContratService,
    private fb: FormBuilder,
    private gestionFournisseurService: GestionFournisseurService,
    private  gestionUserService: GestionUtilisateurService
  ) {
  }


  ngOnInit(): void {
    this.resolveMode();
    if(this.mode === 'UPDATE' ) {
      console.log('mode contrat',this.mode)
      console.log('det contrat',this.contrat?.user?.lastName)
      console.log('id',this.id)
      this.initForm1(this.contrat);
      this.initForm2(this.contrat);
      this.initForm3(this.contrat);
      this.fournisseurs= this.gestionFournisseurService.getAll();
      this.users= this.gestionUserService.getAll();
    }
    if(this.mode === 'CREATE') {
      this.initForm1();
      this.initForm2();
      this.initForm3();
      this.fournisseurs= this.gestionFournisseurService.getAll();
      this.users= this.gestionUserService.getAll();
    }
  }
  initForm1(contrat?: Contrat): void{
    this.formGroup = this.fb.group({
      fournisseur:[contrat?.fournisseur?.nom|| '',Validators.required],
      user:[contrat?.user?.firstName|| '',Validators.required],
      correspondant:[contrat?.correspondant || '',],
      dateValidateKYS:[contrat?.dateValiditeKYS || '',]
    })
  }
  initForm2(contrat?: Contrat): void{
    this.formGroup1 = this.fb.group({
      natureContrat:[contrat?.natureContrat||'',Validators.required],
      refContrat:[contrat?.refContrat||'',Validators.required],
      type:[contrat?.type || '' ],
      contratInitial: [{ value: contrat?.contratInitial || '', disabled: true}],
      leadContrat:[contrat?.leadContrat || '',],
      status:[contrat?.status || '']
    });

    this.formGroup1.get('type')?.valueChanges.subscribe(value =>{
      if(value === Type.AVENANT) {
        this.formGroup1.get('contratInitial')?.enable();
      }
      else {
        this.formGroup1.get('contratInitial')?.disable();
      }
    });
  }


  initForm3(contrat?: Contrat): void{
    this.formGroup2 = this.fb.group({
      dateSignatureContrat:[contrat?.dateSignatureContrat||''],
      dateValiditeContrat:[contrat?.dateValiditeContrat|| ''],
      delaisPaiement:[contrat?.delaisPaiement||  '',],
      montantHt:[contrat?.montantHt|| '',],
      devise:[contrat?.devise||'',],
      file:[ '',],
    })
  }


  private resolveMode(): void {
    this.mode = this.id != null ? 'UPDATE' : 'CREATE';
  }



  get form() {
    return this.formGroup?.controls;
  }

  onReset() {
    this.submitted = false;
    this.formGroup.reset();
    this.formGroup1.reset();
    this.formGroup2.reset();
  }

  onSave() {
    console.log(this.formGroup.value)
    console.log(this.formGroup1.value)
    console.log(this.formGroup2.value)

    const subTitleSucces = "Succée";
    const titleSucces = "Contrat mis à jour avec succés";
    const subTitleError = "Erreur";
    const titleError = "Merci de compléter les champs indiqué";

    this.submitted = true;

    
    if (this.formGroup.invalid) {
      this.utilsService.displayError(subTitleError, titleError);
      return;
    }

    const formValue = this.formGroup.getRawValue();
    const formValue1 = this.formGroup1.getRawValue();
    const formValue2 = this.formGroup2.getRawValue();
    const contrat: Contrat = {
      correspondant: formValue.correspondant,
      dateValiditeKYS: formValue.dateValiditeKYS,
      fournisseur: formValue.fournisseur,
      user : formValue.user,
      natureContrat : formValue1.natureContrat,
      refContrat : formValue1.refContrat,
      type: formValue1.type,
      contratInitial: formValue1.contratInitial,
      leadContrat: formValue1.leadContrat,
      status: formValue1.status,
      dateSignatureContrat: formValue2.dateSignatureContrat,
      dateValiditeContrat: formValue2.dateValiditeContrat,
      delaisPaiement: formValue2.delaisPaiement,
      montantHt: formValue2.montantHt,
      devise: formValue2.devise,
      file: formValue2.file,   
    };
    let observable: Observable<any>;
    if(this.mode === 'UPDATE' && this.id) {
      observable = this.gestionContratService.updateContrat(this.id, contrat);
    }
    else {
      observable = this.gestionContratService.createContrat(contrat);
    }
    observable.subscribe(() =>{
      this.utilsService.displaySucess(subTitleSucces,titleSucces);
      if(this.mode === 'CREATE') {
        this.onReset();
      }
    });
  }

}
