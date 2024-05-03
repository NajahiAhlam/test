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



