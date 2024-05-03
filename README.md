<nb-card>
    <nb-card-header class="d-flex justify-content-between mb-4">
      <div>
        <h5>Budget Analysis</h5>
      </div>
      <div>
        <div class="row">
          <div class="col">
            <nb-select class="my-2" id="application" status="info" placeholder="Select programme" (selectedChange)="changeProgramme($event)" >
              <nb-option value="Toute la Feuille de route">Toute la Feuille de route</nb-option>
              <nb-option *ngFor="let prog of programmes" [value]="prog">{{prog}}</nb-option>
              <!-- <nb-option value="Core Banking System">Core Banking System</nb-option>
              <nb-option value="Buffer">Buffer</nb-option>
              <nb-option value="Data and BI">Data and BI</nb-option>
              <nb-option value="centre de Compètence">centre de Compètence</nb-option> -->
            </nb-select>
          </div>
     <div class="col">
            <nb-select class="my-2" id="type"  placeholder="Select projet" [disabled]= "!selectedProgramme" (selectedChange)="changeProjet($event)" >
              <nb-option *ngFor="let projet of filteredProjets" [value]="projet.name">{{projet.name}}</nb-option>
            </nb-select>
          </div>
          <div class="col">
            <nb-select class="my-2" id="type"  placeholder="Select lead" [disabled]= "!selectedProgramme" (selectedChange)="changeLead($event)" >
                <nb-option *ngFor="let lead of filteredLeads" [value]="lead.name">{{lead.name}}</nb-option>
              </nb-select>
          </div>
          <div class="col">
              <nb-select class="my-2" id="type"  placeholder="Annee budgitaire" >
                <nb-option value="allTypeDemande">2023</nb-option>
                <nb-option value="type">2024</nb-option>
                <nb-option value="type">2025</nb-option>
              </nb-select>
          </div>
         
          <div class="col">
            <button nbButtonToggle value="Mois" pressed>Rechercher</button>
          </div>
        </div>
      </div>
    </nb-card-header>
  
  </nb-card>


  <div class="col-lg-12 ">
     <nb-card> 
        <nb-card-header class="d-flex justify-content-between mb-4"> 
            <div >
                 <h5 class="fs-5">Details J/H</h5> 
            </div> 
        </nb-card-header> 
        <nb-card-body> 
        <table aria-describedby="app-ref" class="w-100 table-hover bg-white"> 
            <thead > 
             
            <tr>
            <th style=" background:#fff; border: #fff;"></th> 
                <th  style="text-align: center;" colspan="2"  >SOL COPRO</th>
                <th  style="text-align: center;"colspan="2">SOL MMA</th>
                <th  style="text-align: center;" colspan="2">SOL RETAIL</th>
                <th  style="text-align: center;"colspan="2"  >SOL RFC</th>
                <th  style="text-align: center;" colspan="2">SOL RH</th>
                <th  style="text-align: center;" colspan="2">SOL BI</th>
                <th  style="text-align: center;" colspan="2"  >SOL PHOENIX</th>
                <th  style="text-align: center;" colspan="2">SOL OP</th>
                <th  style="text-align: center;" colspan="2">DIFA</th>
                <th  style="text-align: center;" colspan="2">ITS</th>
                <th  style="text-align: center;" colspan="2">GOV IT</th>
                <th  style="text-align: center;" colspan="2">COO TECH</th>
                
            </tr>
            <tr>
              <th style=" background:#fff; border: #fff;"></th>
                <th>interne</th>
                <th>externe</th>
            
                <th>interne</th>
                <th>externe</th>
            
                <th>interne</th>
                <th>externe</th>
                
                <th>interne</th>
                <th>externe</th>
                
                <th>interne</th>
                <th>externe</th>
                
                <th>interne</th>
                <th>externe</th>
                
                <th>interne</th>
                <th>externe</th>
                
                <th>interne</th>
                <th>externe</th>
                
                <th>interne</th>
                <th>externe</th>
                
                <th>interne</th>
                <th>externe</th>
                
                <th>interne</th>
                <th>externe</th>
                
                <th>interne</th>
                <th>externe</th>
            </tr>
         </thead>
  <tbody>
    


<tr>
  <td style=" color:gray; "><strong>Initial:</strong>  </td>
  <td>{{interne}}</td>
  <td>{{interne}}</td>
  <td>{{interne}}</td>
  <td>{{interne}}</td>
  <td>123</td>
  <td>123</td>
  <td>123</td>
  <td>123</td>
  <td>123</td>
  <td>{{interne}}</td>
  <td>{{interne}}</td>
  <td>123</td>
  <td>123</td>
  <td>123</td>
  <td>123</td>
  <td>{{interne}}</td>
  <td>{{interne}}</td>
  <td>{{interne}}</td>
  <td>{{interne}}</td>
  <td>123</td>
  <td>123</td>
  <td>123</td>
  <td>123</td>
  <td>123</td>
</tr>
<tr>
  <td style=" color:gray;"><strong>Up To Date:</strong>  </td>
  <td>123</td>
  <td>123</td>
  <td>123</td>
  <td>123</td>
  <td>123</td>
  <td>123</td>
  <td>123</td>
  <td>{{interne}}</td>
  <td>{{interne}}</td>
  <td>{{interne}}</td>
  <td>{{interne}}</td>
  <td>123</td>
  <td>123</td>
  <td>123</td>
  <td>123</td>
  <td>123</td>
  <td>123</td>
  <td>{{interne}}</td>
  <td>{{interne}}</td>
  <td>{{interne}}</td>
  <td>{{interne}}</td>
  <td>123</td>
  <td>123</td>
  <td>123</td>
</tr>
<tr>
  <td style=" color:gray;"><strong> Atterissage: </strong></td>
  <td>{{interne}}</td>
  <td>{{interne}}</td>
  <td>{{interne}}</td>
  <td>{{interne}}</td>
  <td>123</td>
  <td>123</td>
  <td>123</td>
  <td>{{interne}}</td>
  <td>{{interne}}</td>
  <td>{{interne}}</td>
  <td>{{interne}}</td>
  <td>123</td>
  <td>123</td>
  <td>123</td>
  <td>123</td>
  <td>123</td>
  <td>123</td>
  <td>123</td>
  <td>123</td>
  <td>123</td>
  <td>123</td>
  <td>123</td>
  <td>123</td>
  <td>123</td>
</tr>
    </tbody>
  </table>
        </nb-card-body>
     </nb-card>
  </div>

<section class="d-flex justify-content-between" >
  <div  class="col-lg-6" style="width: 48%;">
    <nb-card> 
       <nb-card-header class="d-flex justify-content-between mb-4"> 
           <div >
                <h5 class="fs-5">Details CTB</h5> 
           </div> 
           
       </nb-card-header> 
       <nb-card-body> 
       <table aria-describedby="app-ref" class="w-100 table-hover bg-white"> 
           <thead > 
            
           <tr>
           <th style=" background:#fff; border: #fff;"></th> 
               <th  style="text-align: center;"  >REGIE</th>
               <th  style="text-align: center;">SGABS</th>
               <th  style="text-align: center;">LOGICIEL</th>
               <th  style="text-align: center;">FORFAIT</th>
               <th  style="text-align: center;" >TOTAL</th>
               
        </thead>
 <tbody>
   


<tr>
 <td style=" color:gray; "><strong>Initial:</strong>  </td>
 <td>{{REGIE}}</td>
 <td>{{SGABS}}</td>
 <td>{{LOGICIEL}}</td>
 <td>{{FORFAIT}}</td>
 <td>{{TOTAL}}</td>
</tr>
<tr>
 <td style=" color:gray;"><strong>Up To Date:</strong>  </td>
 <td>{{REGIE}}</td>
 <td>{{SGABS}}</td>
 <td>{{LOGICIEL}}</td>
 <td>{{FORFAIT}}</td>
 <td>{{TOTAL}}</td>
</tr>
<tr>
 <td style=" color:gray;"><strong> Atterissage: </strong></td>
 <td>{{REGIE}}</td>
 <td>{{SGABS}}</td>
 <td>{{LOGICIEL}}</td>
 <td>{{FORFAIT}}</td>
 <td>{{TOTAL}}</td>

</tr>
   </tbody>
 </table>
 <div class=" text-danger download-btn" style="float:right; position: relative; right:0;margin-top:5%" (click)="downloadImage()">
  Télécharger
  <nb-icon class="ms-2" style="line-height: 20px;" icon="download"></nb-icon>
</div>
       </nb-card-body>
     
    </nb-card>
  
    
 </div>



 <div class="col-lg-6"  >
  <nb-card style="height: 389px;"> 
     <nb-card-header class="d-flex justify-content-between m-0"> 
         <div style="width:100%" >
              <h5 class="fs-5">Solution Budgitaire à date </h5> 
           
                <nb-select style="float: right;" class="m-0 " id="type" (selectedChange)="changeLocation($event)" value="" selected  >Prestation Régie
                    <nb-option value="Prestation Régie">Prestation Régie</nb-option>
                    <nb-option value="Prestation SGABS">Prestation SGABS</nb-option>
                    <nb-option value="Matériel-logiciel">Matériel-logiciel</nb-option>
                    <nb-option value="Prestation Forfait">Prestation Forfait</nb-option>
                  </nb-select>
            
           
         </div> 
     </nb-card-header> 
     <nb-card-body> 
     <table #screen aria-describedby="app-ref" class="w-100 table-hover bg-white" id="prestation-table"> 
         <thead > 
          
         <tr>
             <th>Alloué</th>
             <th>Engagé</th>
             <th>Target</th>
             <th>Consommé</th>
             <th>Target</th>
             <th>Facturé</th>
             <th>Target</th>

             
        
            </tr>
      </thead>
      <tbody>
        <tr>
            <td>{{ allocated }}</td>
            <td>{{ engaged }}</td>
            <td>{{ target1 }}</td>
            <td>{{ consumed }}</td>
            <td>{{ target2 }}</td>
            <td>{{ billed }}</td>
            <td>{{ target3 }}</td>
        </tr>
    </tbody>
</table>
    
   <div class=" text-danger download-btn" style="float:right; position: relative; right:0;margin-top:5%" (click)="downloadImage()">
      Télécharger
      <nb-icon class="ms-2" style="line-height: 20px;" icon="download"></nb-icon>
    </div>
     </nb-card-body>
     
  </nb-card>
    <div style="display: none;" id="download">
              <img #canvas>
              <a #downloadLink></a>
            </div>
    </div>
</section>



------------------------------------------------------------------------------------------
import { Component, ElementRef, OnInit, ViewChild } from '@angular/core';
import html2canvas from 'html2canvas';

@Component({
  selector: 'app-budget-analysis',
  templateUrl: './budget-analysis.component.html',
  styleUrls: ['./budget-analysis.component.scss']
})
export class BudgetAnalysisComponent implements OnInit {
  @ViewChild("screen") screen!: ElementRef;
  @ViewChild("canvas") canvas!: ElementRef;
  @ViewChild("downloadLink") downloadLink!: ElementRef;
  selectedPrestation !: string;
  selectedProgramme : string ='';
  selectedProjets : string ='';
  selectedLead : string='';
  programmes: string[] = ['core baning systeme', 'BUFFER','CENTRE DE COMPETENCE'];
  projets:{name: string, program : string} [] =[
    {name:'Projet1', program:'core baning systeme'},
    {name:'Projet2', program:'core baning systeme'},
    {name:'Projet3', program:'core baning systeme'},
    {name:'Projet4', program:'BUFFER'},
    {name:'Projet5', program:'BUFFER'},
    {name:'Projet6', program:'CENTRE DE COMPETENCE'},
   
    ];
    leads: {name: string, program : string} [] =[
      {name:'lead1', program:'core baning systeme'},
      {name:'lead2', program:'core baning systeme'},
      {name:'lead3', program:'core baning systeme'},
      {name:'lead4', program:'BUFFER'},
      {name:'lead5', program:'BUFFER'},
      {name:'lead6', program:'CENTRE DE COMPETENCE'},
    ];
  filteredProjets: {name: string, program : string} [] = [];
  filteredLeads: {name: string, program : string} [] = [];
  changeLead(selected: string): void{
    this.selectedProgramme=selected;
    this.selectedLead='';
    this.filteredLeads= this.leads.filter(lead=>lead.program ===selected)
       this.selectedLead='';
  }

    allocated = 100; 
    engaged = 80;
    target1= 90;
    consumed=100;
    target2=45;
    billed=5687;
    target3=34567


  REGIE : number=20000000; 
  SGABS: number= 300000000; 
  LOGICIEL: number= 56898; 
  FORFAIT: number= 7357456;
  TOTAL: number= 976385; 
  interne: number= 345678;
  externe: number = 987654;
  changeProjet(selected: string): void{
    this.selectedProjets = selected;
  }
  changeProgramme(selected: string):void{
    this.selectedProgramme=selected;
    this.selectedProjets='';
    this.filteredProjets= this.projets.filter(projet =>projet.program ===selected);
       this.selectedProjets='';
    
  }
  changeLocation(selected: string): void {
    this.selectedPrestation = selected;
    if (selected === 'Prestation Régie') {
       this.allocated = 100; 
      this.engaged = 80;
      this.target1= 90;
      this.consumed=100;
      this.target2=45;
      this.billed=5687;
      this.target3=34567
    }
     
       
    else if (selected === 'Prestation SGABS') {
      this.allocated = 123600; 
      this.engaged = 809876;
      this.target1= 9056789;
      this.consumed=100;
      this.target2=45;
      this.billed=5687;
      this.target3=34567
    }
    else if (selected === 'Matériel-logiciel') {
      this.allocated = 123456789000; 
      this.engaged = 80;
      this.target1= 90;
      this.consumed=100;
      this.target2=45;
      this.billed=5687;
      this.target3=34567
  }
  else if (selected === 'Prestation Forfait') {
    this.allocated = 102345678900; 
    this.engaged = 80;
    this.target1= 90;
    this.consumed=100;
    this.target2=45;
    this.billed=5687;
    this.target3=34567
  }
}
downloadImage() {
 
  html2canvas(this.screen.nativeElement).then(canvas => {
    this.canvas.nativeElement.src = canvas.toDataURL();
    this.downloadLink.nativeElement.href = canvas.toDataURL("image/png");
    this.downloadLink.nativeElement.download = "liste_projets.png";
    this.downloadLink.nativeElement.click();
  });
}

  constructor() { }

  ngOnInit(): void {
  }

}
  

