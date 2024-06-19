

<!-- <section class="row d-flex justify-content-between"> -->
<!-- Search Bar  -->
  <!-- <nb-card>
    <nb-card-header class="d-flex justify-content-between mb-4">
      <div>
        <h5 >Rapports financiers   >  <span style="color: gray; font-style: normal;">Billing Tracking</span> </h5>
      </div>
      <div class="row">
              <div class="col"  >
                <section style="float: right;"class="d-flex justify-content-between">
                  <div class="mx-5">
                    <nb-select class=" bg-grey text-bold" placeholder="Focus Fournisseur" >
                      <nb-option value="SOPRA">SOPRA</nb-option>
                    </nb-select>
                  </div>
                  <div>
                    <nb-select  placeholder="Intervenant">
                      <nb-option value="NABIL Zakaria">NABIL Zakaria</nb-option>
                      <nb-option value="NAJAHI Ahlam">NAJAHI Ahlam</nb-option>
                  </nb-select>
                  </div>
                  <div class="mx-5">
                    <nb-select placeholder="Date Chargement : 2023">
                        <nb-option value="2024-01-01">2024-01-01</nb-option>
                    </nb-select>
                  </div>
                  <div class="col" style="">
                    <button nbButtonToggle  pressed>Rechercher</button>
                </div>
               </section>

          
       
      </div>
      </div>
    </nb-card-header>
    </nb-card>
   
    <nb-card class="col-lg-6" >
      <nb-card-header class="d-flex justify-content-between mb-4">
          <h5>Chiffres Clés :</h5>
           <nb-select  placeholder="Status">
               <nb-option value="En Cours">En cours</nb-option>
               <nb-option value="Réglée">Réglée</nb-option>
               <nb-option value="Annulée">Annulée</nb-option>
           </nb-select>
        </nb-card-header>
  
      <div  class="d-flex justify-content-between m-3">
      
        <div class="col-lg-3  p-1">
        <div class="card small-cards shadow mb-4 bg-white "  >
         <div class="card-body d-flex flex-column ">
           <div class="icon-row1 d-flex justify-content-center align-items-center    ">
             <nb-icon icon="file-text"   style="font-size: 40px;"></nb-icon>
           </div>
           <div class="title-row mt-2 pb-0">
            <p class="fs-5">Nombre total factures  </p>
          </div>
           <div class="stats-row align-self-end justify-content-start pb-0">
             <h5 style="color:#e9041e">150</h5>
           </div>
   
         </div>
   
       </div>
     </div> 
   
   
   
    
      <div class="col-lg-4  p-1">
   
       <div class="card  small-cards shadow mb-5 bg-white "  >
         <div class="card-body d-flex flex-column ">
           <div class="icon-row4 d-flex justify-content-center align-items-center">
             <nb-icon icon="file-invoice"   style="font-size: 40px;">
             </nb-icon>
           </div>
   
           <div class="title-row mt-2 pb-0">
             <p class="fs-5">Nombre Factures Pénalités </p>
           </div>
           <div class="stats-row align-self-end justify-content-start pb-0">
             <h4 style="color:#e9041e">5</h4>
           </div>
   
         </div>
   
   
       </div>
     </div>
   
   
   <div class="col-lg-4 p-1">
   
       <div class="card small-cards shadow mb-5 bg-white "  >
         <div class="card-body d-flex flex-column ">
           <div class="icon-row2 d-flex justify-content-center align-items-center">
             <nb-icon icon="sack-dollar"   style="font-size: 40px;"> </nb-icon>
           </div>
   
   
           <div class="title-row mt-2 pb-0">
             <p class="fs-5">Volume Factures (MAD) </p>
           </div>
           <div class="stats-row align-self-end justify-content-start pb-0">
             <h4 style="color:#e9041e">279 000 MAD</h4>
           </div>
   
         </div>
   
   
   
       </div>
      </div>   
    </div>
  
   </nb-card>
  

   <nb-card class="col-lg-6 ">
      <nb-card-header class="d-flex justify-content-between ">
        <div >
          <h5>Délais moyen - Facture réglées :</h5>
        </div>
        
      </nb-card-header>
  
      <section  class="d-flex justify-content-between m-5">
      <div class="col-lg-5  ">
        <div class="card small-cards shadow mb-5 bg-white "  >
         <div class="card-body d-flex flex-column ">
           <div class="icon-row1 d-flex justify-content-center align-items-center">
   
             <nb-icon icon="globe"   style="font-size: 40px;"> </nb-icon>
           </div>
   
           <div class="title-row mt-4 pb-0">
            <p class="fs-5">Traitement Global</p>
           </div>
           <div class="stats-row align-self-end justify-content-start pb-0">
             <h4 style="color:#e9041e">120</h4>
           </div>
         </div>
   
       </div>
     </div> 
     <div class="col-lg-5">
   
       <div class="card  small-cards shadow  bg-white "  >
         <div class="card-body d-flex flex-column ">
           <div class="icon-row4 d-flex justify-content-center align-items-center">
             <nb-icon icon="gears"   style="font-size: 40px;"> </nb-icon>
           </div>
   
           <div class="title-row mt-2 pb-0">
             <p class="fs-5">Traitement jusqu'approbation</p>
           </div>
           <div class="stats-row align-self-end justify-content-start pb-0">
             <h4 style="color:#e9041e"> 16</h4>
           </div>
         </div>
       </div>
     </div>
    </section>
  
   </nb-card>

    <nb-tabset>
   
      <nb-tab tabTitle="Chiffres Clés">
        <app-chiffres-cles></app-chiffres-cles>
      </nb-tab>
  
      <nb-tab tabTitle ="Top 10 Fournisseurs">
          <app-top-fournisseur></app-top-fournisseur>
      </nb-tab>
   
      <nb-tab tabTitle="Factures en pénalité">
       <app-factures-en-penalite></app-factures-en-penalite>
      </nb-tab>     
    </nb-tabset>

</section> -->



<nb-card accent="primary">
  <ng-container>
    <ng-container>
      <!-- <rpa-portal-loading></rpa-portal-loading> -->
    </ng-container> 
    <ng-container>
      <nb-card-body>
        <!-- <div class="alert alert-danger" role="alert"></div> -->
      </nb-card-body>
    </ng-container>
    <ng-container>
      <nb-card-header class="d-flex flex-row justify-content-between">
        <h5 class="title-animation title-heading text-uppercase my-auto p-2">Billing Tracking</h5>
        <nb-select placeholder="date de chargement">
          <nb-option value="2024-01-01">2024-01-01</nb-option>
        </nb-select>
      </nb-card-header>
      <nb-card-body>
        <div class="row d-flex flex-row justify-content-around mt-2">
          <!-- Cards for different metrics -->
          <!-- Card 1 -->
          <div class="col-lg-2 my-5">
            <div class="card small-cards shadow mb-5 bg-white">
              <div class="card-body d-flex flex-column">
                <div class="icon-row1 d-flex justify-content-center align-items-center shadow">
                  <nb-icon icon="list" style="font-size: 40px;"></nb-icon>
                </div>
                <div class="title-row mt-2 pb-0">
                  <p class="fs-5">Nombre Total Factures</p>
                </div>
                <div class="stats-row align-self-end justify-content-start pb-0">
                  <h4>133</h4>
                </div>
              </div>
            </div>
          </div>
          <!-- Card 2 -->
          <div class="col-lg-2 my-5">
            <div class="card small-cards shadow mb-5 bg-white">
              <div class="card-body d-flex flex-column">
                <div class="icon-row2 d-flex justify-content-center align-items-center shadow">
                  <nb-icon icon="truck" pack="fas" style="font-size: 40px;"></nb-icon>
                </div>
                <div class="title-row mt-2 pb-0">
                  <p class="fs-5">Nombre Factures Pénalités</p>
                </div>
                <div class="stats-row align-self-end justify-content-start pb-0">
                  <h4>0</h4>
                </div>
              </div>
            </div>
          </div>
          <!-- Card 3 -->
          <div class="col-lg-2 my-5">
            <div class="card small-cards shadow mb-2 bg-white">
              <div class="card-body d-flex flex-column">
                <div class="icon-row4 d-flex justify-content-center align-items-center shadow">
                  <nb-icon icon="check" pack="fas" style="font-size: 40px;"></nb-icon>
                </div>
                <div class="title-row mt-2 pb-0">
                  <p class="fs-5">Volumne Factures (MAD)</p>
                </div>
                <div class="stats-row align-self-end justify-content-start pb-0">
                  <h4>3</h4>
                </div>
              </div>
            </div>
          </div>
          <!-- Card 4 -->
          <div class="col-lg-2 my-5">
            <div class="card small-cards shadow mb-5 bg-white">
              <div class="card-body d-flex flex-column">
                <div class="icon-row2 d-flex justify-content-center align-items-center shadow">
                  <nb-icon icon="money-bill" pack="fas" style="font-size: 40px;"></nb-icon>
                </div>
                <div class="title-row mt-2 pb-0">
                  <p class="fs-5">Budget</p>
                </div>
                <div class="stats-row align-self-end justify-content-start pb-0">
                  <h4>177,690.6</h4>
                </div>
              </div>
            </div>
          </div>
        </div>
        <nb-card class="mt-4 col-lg-6" style="padding:2%">
          <nb-card-header  class="key">
            <h5 class="fs-5">Factures par Status/Mois (Uniquement les 7 derniers mois) :</h5>
          </nb-card-header>
          <nb-card-body>
            <canvas #barChart id="barChart" style="position: relative;"></canvas>
          </nb-card-body>
        </nb-card>
          <div id="divBarChart" class="mt-4 col-lg-4" style="padding:2%">
            <h5 class="fs-5">Factures par Status/Mois (Uniquement les 7 derniers mois) :</h5>
            <highcharts-chart
              [Highcharts]="Highcharts"
              [options]="chartOptions"
              style="width: 100%; height: 400px; display: block;">
            </highcharts-chart>
          </div>
          <nb-card  class="mt-4 col-lg-6" style="padding:2%">
            <nb-card-header class="key">
          <h5 class="fs-5">Factures Distribution :</h5>
            </nb-card-header>
            <nb-card-body>
                      <highcharts-chart
                        [Highcharts]="Highcharts"
                        [options]="pieChartOptions"
                        style="width: 100%; height: 400px; display: block;">
                      </highcharts-chart>
            </nb-card-body>
          </nb-card>
          <nb-card>
            <nb-card-header>
              <h5 class="fs-5">Factures Distribution :</h5>
            </nb-card-header>
            <nb-card-body>
                 <highcharts-chart
          [Highcharts]="Highcharts"
          [options]="barChartOptions"
          style="width: 100%; height: 400px; display: block;">
        </highcharts-chart>
            </nb-card-body>
          </nb-card>

          <nb-card>
            <nb-card-header>
              <h5 class="fs-5">Factures Distribution :</h5>
            </nb-card-header>
            <nb-card-body>
                 <highcharts-chart
          [Highcharts]="Highcharts"
          [options]="chartOptions1"
          style="width: 100%; height: 400px; display: block;">
        </highcharts-chart>
            </nb-card-body>
          </nb-card>
       
        
         
      
      </nb-card-body>
    </ng-container>
  </ng-container>
</nb-card>
