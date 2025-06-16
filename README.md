export class DemandePeopleComponent {
  peopoleCardBody: boolean = true;

  @Input() data!: DemandeQualificationResponse | undefined;

  sponsorNames: string = '';
  reporterNames: string = '';
  cordinateurNames: string[] = []; // Store as array instead of a concatenated string

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
      person.assignName === this.data.assignName;
  }

  hideShowPeopoleCardBody() {
    this.peopoleCardBody = !this.peopoleCardBody;
  }
}

<ul *ngIf="cordinateurNames.length">
  <li *ngFor="let person of data.peoples">
    <span [ngClass]="{ highlight: isCoordinatorHighlight(person) }">
      {{ person.assignName }}
    </span>
  </li>
</ul>
.highlight {
  background-color: green;
  color: #ffffff;
  font-weight: bold;
}
