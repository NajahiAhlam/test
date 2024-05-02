 <div class="col-lg-6 ">
  <nb-card> 
     <nb-card-header class="d-flex justify-content-between m-0"> 
         <div style="width:100%" >
              <h5 class="fs-5">Solution Budgitaire à date </h5> 
           
                <nb-select style="float: right;" class="m-0 " id="type"  placeholder="Select lead" (selectedChange)="changeLocation($event)" >
                    <nb-option value="Prestation Régie">Prestation Régie</nb-option>
                    <nb-option value="Prestation SGABS">Prestation SGABS</nb-option>
                    <nb-option value="Matériel-logiciel">Matériel-logiciel</nb-option>
                    <nb-option value="Prestation Forfait">Prestation Forfait</nb-option>
                  </nb-select>
            
           
         </div> 
     </nb-card-header> 
     <nb-card-body> 
     <table aria-describedby="app-ref" class="w-100 table-hover bg-white" id="prestation-table"> 
         <thead > 
          
         <tr>
             <th  style="text-align: center;" colspan="7" id="changeth">{{selectedPrestation}} </th>
                    
         </tr>
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
<tbody id="table-body">

<!-- <tr>
<td>165238</td>
<td>164671</td>
<td>100%</td>
<td>140736,6535</td>
<td>85</td>
<td>123</td>
<td>0 %</td>

<td>33119,4034820116</td>
<td>41645,8610755814</td>
<td>126 %</td>
<td>17274</td>
<td>123</td>
<td>123</td>
<td>0%</td>

<td>15628,05802</td>
<td>17091,4827131783</td>
<td>109 %</td>
<td>17274</td>
<td>109 %</td>
<td>123</td>
<td>0%</td>

<td>131454,80009608</td>
<td>110383,492209302</td>
<td>84 %</td>
<td>110383,49220930</td>
<td>84 %</td>
<td>123</td>
<td>0%</td>

</tr> -->

 </tbody>
</table>
     </nb-card-body>
  </nb-card>
</div>


import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-budget-analysis',
  templateUrl: './budget-analysis.component.html',
  styleUrls: ['./budget-analysis.component.scss']
})


export class BudgetAnalysisComponent implements OnInit {
  selectedPrestation !: string;
  changeLocation(selected: string): void {
    this.selectedPrestation = selected;
    
  }
  

  constructor() { }

  ngOnInit(): void {
  }
  }
