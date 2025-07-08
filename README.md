export class DemandePeopleComponent {
  peopoleCardBody: boolean = true;
  @Input() data!: DemandeQualificationResponse | undefined;
  sponsorNames: string = '';
  reporterNames: string = '';
  cordinateurNames: string[]  = []; 


  constructor() { }

  ngOnInit(): void {
    console.log('"data peosi', this.data?.reporter)
    if (this.data) {
      this.formatNames();
    }
  }

  formatNames() {
    this.sponsorNames = this.getNamesByType('SPONSOR');
    this.reporterNames = this.getNamesByType('REPORTER');
    //this.cordinateurNames = this.getNamesByType('COORDINATOR');
     this.cordinateurNames = this.getCordinateurNames();

}

  getNamesByType(type: string): string {
    if (!this.data || !this.data.peoples) {
        return '';
    }
    const filtered = this.data.peoples.filter(people => people.type === type);
    const nameCounts = filtered.reduce((acc, person) => {
        acc[person.assignName] = (acc[person.assignName] || 0) + 1;
        return acc;
    }, {} as { [key: string]: number });

    return Object.entries(nameCounts)
        .map(([name, count]) => count > 1 ? `${name} (x${count})` : name)
        .join(', ');
}
  getCordinateurNames(): string[] {
    if (!this.data?.peoples) return [];

    return this.data.peoples
      .filter(p => p.type === 'COORDINATOR')
      .map((p) => p.assignName);
  }

  isCoordinatorHighlight(person: any): boolean {
    return person.type === 'COORDINATOR' &&
      person.assignName === this.data?.assigneeName;
  }


  hideShowPeopoleCardBody() {
    this.peopoleCardBody = !this.peopoleCardBody;
  }


}
<nb-card>
    <nb-card-header >
      <div class="d-flex flex-row justify-content-between">
        <div>Personnes</div>
        <div>
          <a (click)="hideShowPeopoleCardBody()" *ngIf="peopoleCardBody">
            <nb-icon  icon="chevron-up"></nb-icon>
          </a>
          <a (click)="hideShowPeopoleCardBody()" *ngIf="!peopoleCardBody">
            <nb-icon  icon="chevron-down"></nb-icon>
          </a>
        </div>
      </div>
    </nb-card-header>
    <nb-card-body *ngIf="peopoleCardBody">
      <!--<div class="key-value-pair" *ngIf="data?.assigne">
        <div class="key col-6">Responsable:</div>
         <div class="value col-6">{{data?.assigne?.lastName}} {{data?.assigne?.firstName}}</div> 
      </div>-->
      <!-- <div class="key-value-pair">
        <div class="key col-6">Initiateur:</div>
         <div class="value col-6">{{data?.reporterName}}</div>
      </div> -->
      <div class="key-value-pair" *ngIf="reporterNames">
        <div class="key col-6">Initiateur:</div>
        <div class="value col-6">{{ reporterNames }}</div>
      </div>
      <div class="key-value-pair" *ngIf="cordinateurNames">
        <div class="key col-6">Coordinateur:</div>
        <div class="value col-6">{{ cordinateurNames }}</div>
      </div>
 
      <div class="key-value-pair" *ngIf="sponsorNames">
        <div class="key col-6">Sponsor:</div>
        <div class="value col-6">{{ sponsorNames }}</div>
      </div>
  
      <div class="key-value-pair" >
        <div class="key col-6">Porteur Métier:</div>
        <div class="value col-6">{{ data?.porteurMetier }}</div>
      </div>
      <div class="key-value-pair" >
        <div class="key col-6">Porteur Projet:</div>
        <div class="value col-6">{{ data?.porteurProjet }}</div>
      </div>
      <!-- <div class="key-value-pair" *ngIf="data?.sponsort">
        <div class="key col-6">Sponsor:</div>
        <div class="value col-6">{{ data?.sponsort }}</div>
      </div> -->
    </nb-card-body>
  </nb-card>
  hey chat i have table people where i stock all the initiateur coordinateur, sponsor i want if like there is more than one in coordinateur the color the one who is 
equals to data.coordinateur and same for the others 
