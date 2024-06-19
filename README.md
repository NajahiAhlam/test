<nb-card accent="primary">
  <ng-container>
    <ng-container>
      <!-- <rpa-portal-loading></rpa-portal-loading> -->
    </ng-container> 
   
    <ng-container>
      <nb-card-body>
        <!-- <div class="alert alert-danger" role="alert">
          
        </div> -->
      </nb-card-body>
    </ng-container>
    <ng-container >
      <nb-card-header class="d-flex flex-row justify-content-between">
        <h5 class="title-animation title-heading text-uppercase my-auto p-2">Billing Tracking
        </h5>
        <nb-select placeholder="date de chargement">
          <nb-option value="2024-01-01">2024-01-01</nb-option>
      </nb-select>
       
      </nb-card-header>
      <nb-card-body>
        <div class="row d-flex flex-row justify-content-around mt-2"> 


          <!-- ******** card 1 ******** -->
         <div class="col-lg-2 my-5">
              <div class="card small-cards shadow mb-5 bg-white "  >
               <div class="card-body d-flex flex-column ">
                 <div class="icon-row1 d-flex justify-content-center align-items-center shadow   ">
         
                   <nb-icon icon="list" style="font-size: 40px;">
         
                   </nb-icon>
                 </div>
         
                 <div class="title-row mt-2 pb-0">
                   <p class="fs-5">Nombre Total Factures </p>
                 </div>
                 <div class="stats-row align-self-end justify-content-start pb-0">
                   <h4>133</h4>
                 </div>
         
               </div>
         
             </div>
          </div>

               <!-- ******** card 2 ******** -->
    <div class="col-lg-2 my-5">
      <div class="card small-cards shadow mb-5 bg-white " >
        <div class="card-body d-flex flex-column ">
          <div class="icon-row2 d-flex justify-content-center align-items-center shadow   ">
            <nb-icon icon="truck" pack="fas" style="font-size: 40px;">
  
            </nb-icon>
          </div>
  
          <div class="title-row mt-2 pb-0">
            <p class="fs-5">Nombre Factures Pénalités</p>
          </div>
          <div class="stats-row align-self-end justify-content-start pb-0">
            <h4> 0</h4>
          </div>
  
        </div>
  
      </div>
    </div> 
  
  
    <!-- ******** card 3 ******** -->
    <div class="col-lg-2 my-5">
  
      <div class="card  small-cards shadow mb-2 bg-white "  >
        <div class="card-body d-flex flex-column ">
          <div class="icon-row4 d-flex justify-content-center align-items-center shadow   ">
            <nb-icon icon="check" pack="fas"  style="font-size: 40px;">
  
            </nb-icon>
          </div>
  
          <div class="title-row mt-2 pb-0">
            <p class="fs-5">Volumne Factures (MAD)</p>
          </div>
          <div class="stats-row align-self-end justify-content-start pb-0">
            <h4> 3</h4>
          </div>
  
        </div>
  
  
      </div>
    </div>  
  
  
    <!-- ******** card 4 ******** -->
      <div class="col-lg-2 my-5">
  
      <div class="card small-cards shadow mb-5 bg-white "  >
        <div class="card-body d-flex flex-column ">
          <div class="icon-row2 d-flex justify-content-center align-items-center shadow">
            <nb-icon icon="money-bill" pack="fas"  style="font-size: 40px;">
  
            </nb-icon>
          </div>
  
  
          <div class="title-row mt-2 pb-0">
            <p class="fs-5"> Budget  </p>
          </div>
          <div class="stats-row align-self-end justify-content-start pb-0">
            <h4>   177 690,6 </h4>
          </div>
  
        </div>
  
      </div>

      </div>
      </div>


      <div id="divBarChart" class="mt-4 col-lg-4" style="padding:2%">
        <h5 class="fs-5">Factures par Status/Mois (Uniquement les 7 derniers mois) : </h5>
        <canvas #barChart id="barChart" style=" position: relative;"></canvas>
      </div>
      </nb-card-body>
      
    </ng-container>
  </ng-container>
</nb-card>
<ng-container >
</ng-container>
--------------------------------------------
chartElementRef !: ElementRef<HTMLCanvasElement>;
  @ViewChild('barChart') barChartCanvas!: ElementRef<HTMLCanvasElement>;
   ngAfterViewInit(): void {
    this.createbarChart()
  }
  -------------------------
    createbarChart(){
    const ctx = this.barChartCanvas.nativeElement;
    new Chart(ctx, {
      type: 'bar',
      data: {
        labels: ['Abondonnees', 'En Cours', 'Non Lances', 'Clotures','','','',''],
        datasets: [{
          label: 'My First Dataset',
          data: [65, 59, 80, 81, 56, 55, 40],
          backgroundColor: [
            'rgba(255, 99, 132, 0.2)',
            'rgba(255, 159, 64, 0.2)',
            'rgba(255, 205, 86, 0.2)',
            'rgba(75, 192, 192, 0.2)',
            'rgba(54, 162, 235, 0.2)',
            'rgba(153, 102, 255, 0.2)',
            'rgba(201, 203, 207, 0.2)'
          ],
          borderColor: [
            'rgb(255, 99, 132)',
            'rgb(255, 159, 64)',
            'rgb(255, 205, 86)',
            'rgb(75, 192, 192)',
            'rgb(54, 162, 235)',
            'rgb(153, 102, 255)',
            'rgb(201, 203, 207)'
          ],
          borderWidth: 1
        }]
      },
    });
  }
  -------
ERROR TypeError: Cannot read properties of null (reading 'id')
    at new Chart (chart.js:5622:175)
    at BillingTrackingComponent.renderBar (billing-tracking.component.ts:258:20)
    at BillingTrackingComponent.ngAfterViewInit (billing-tracking.component.ts:65:10)
    at callHookInternal (core.mjs:4024:14)
    at callHook (core.mjs:4051:13)
    at callHooks (core.mjs:4006:17)
    at executeInitAndCheckHooks (core.mjs:3956:9)
    at refreshView (core.mjs:13569:21)
    at detectChangesInView (core.mjs:13663:9)
    at detectChangesInEmbeddedViews (core.mjs:13606:13)
