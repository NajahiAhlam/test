export class SuivitConditionComponent implements OnInit, AfterViewInit {
  showColumnContextMenu: boolean = false;

  @ViewChild(MatPaginator) paginator!: MatPaginator;
  @ViewChild(MatSort) sort!: MatSort;

  filterValues: { [key: string]: string } = {};

  // MatTableDataSource utilisée pour la table Angular Material
  dataSource: MatTableDataSource<SuivitConditionDto> = new MatTableDataSource();

  // Définition des colonnes affichées dans le tableau
  columns: Array<{ label: string; field: string }> = [
    { label: 'Produit N°', field: 'numProduit' },
    { label: 'Nom du produit', field: 'nomProduit' },
    { label: 'Statut du produit', field: 'statusProduit' },
    { label: 'Présenté le', field: 'dateProduit' },
    { label: 'Decision cnp le', field: 'dateDecisionCnp' },
    { label: 'Produit traité', field: 'traite' },
    { label: 'Produit traité le', field: 'dateTraitement' },
    { label: "'Traité le' mis à Oui le", field: 'dateMSJTraitement' },
    { label: 'Nom du Sponsor', field: 'nomSponsor' },
    { label: 'Département du Sponsor', field: 'departementSponsor' },
    { label: 'Approval Status', field: 'approvalStatus' },
    { label: 'Type de Comité', field: 'typeComite' },
    { label: 'Pré-condition ouverte', field: 'nbrPreConditionOuvert' },
    { label: 'Pré-condition ouverte sur produit traité', field: 'nbrPreConditionSurProduitTraite' },
    { label: 'Post-condition ouverte', field: 'nbrPostConditionOuvert' },
    { label: 'Post-condition échue', field: 'nbrPostConditionEchue' },
    { label: 'Post-condition échue sur produit traité', field: 'nbrPostConditionEchueSurProduitTraite' },
    { label: 'Post-condition arrivant à échéance (1 mois)', field: 'nbrPostConditionEcheance1Mois' },
    { label: 'Nom Assigne', field: 'nomAssigne' }
  ];

  // Liste des colonnes actuellement affichées dans la table
// Liste des colonnes affichées par défaut (les 5 premières)
  displayColumns: string[] = this.columns.slice(0, 11).map(col => col.field);

  // Propriétés personnalisées pour le tri
  sortValue: string = '';      // clé de la colonne de tri active
  sortDirection: boolean = true;  // true = ascendant, false = descendant

  // Tableau d'états issus d'autres requêtes (ici, PRECONDITION et POSTCONDITION)
  etats: any[] = [];

  constructor(private statusService: SuivitConditionService) {}

  ngOnInit(): void {
    // Récupération des infos (POSTCONDITION et PRECONDITION)
    this.statusService.getProductData().subscribe(data => {
      this.etats = [];

      // Traitement de "postcondition"
      if (data && data[ConditionCategory.POSTCONDITION]) {
        Object.keys(data[ConditionCategory.POSTCONDITION]).forEach(key => {
          this.etats.push({
            id: key,
            title: `POSTCONDITION - ${key}`,
            total: data[ConditionCategory.POSTCONDITION][key]
          });
        });
      }

      // Traitement de "précondition"
      if (data && data[ConditionCategory.PRECONDITION]) {
        Object.keys(data[ConditionCategory.PRECONDITION]).forEach(key => {
          this.etats.push({
            id: key,
            title: `PRECONDITION - ${key}`,
            total: data[ConditionCategory.PRECONDITION][key]
          });
        });
      }
    });

    this.loadData();
  }

  ngAfterViewInit(): void {
    this.dataSource.paginator = this.paginator;
  }

  /**
   * Applique un filtre dynamique sur le tableau.
   */
  applyFilter(event: Event): void {
    const input = event.target as HTMLInputElement;
    this.filterValues[input.name] = input.value.trim().toLowerCase();
    // Met à jour le filtre en convertissant l'objet de filtres en chaîne JSON
    this.dataSource.filter = JSON.stringify(this.filterValues);
  }

  /**
   * Charge les données depuis le service et configure le paginator, le tri et le filtre.
   */
  loadData(): void {
    this.statusService.getAll().subscribe((data: SuivitConditionDto[]) => {
      this.dataSource = new MatTableDataSource(data);

      // FilterPredicate personnalisé pour vérifier chaque filtre sur chaque colonne
      this.dataSource.filterPredicate = (data: SuivitConditionDto, filter: string): boolean => {
        const searchTerms = JSON.parse(filter);
        let match = true;
        Object.keys(searchTerms).forEach(key => {
          if (searchTerms[key]) {
            let dataValue = data[key];
            if (typeof dataValue === 'boolean') {
              dataValue = dataValue ? 'true' : 'false';
            }
            dataValue = dataValue ? dataValue.toString().toLowerCase() : '';
            if (!dataValue.includes(searchTerms[key])) {
              match = false;
            }
          }
        });
        return match;
      };

      // Délai pour le rendu avant d'associer le paginator et le tri
      setTimeout(() => {
        this.dataSource.paginator = this.paginator;
        this.dataSource.sort = this.sort;
      });
    });
  }

  /**
   * Méthode de tri personnalisée.
   * Inverse la direction si le tri est déjà appliqué sur la colonne, sinon l'active en mode ascendant.
   */
  sortBy(columnKey: string): void {
    if (this.sortValue === columnKey) {
      this.sortDirection = !this.sortDirection;
    } else {
      this.sortValue = columnKey;
      this.sortDirection = true;
    }
    this.sortData();
  }

  /**
   * Trie les données en fonction de la colonne active et de la direction choisie.
   */
  private sortData(): void {
    if (!this.sortValue) return;
    const data = this.dataSource.data.slice(); // Création d'une copie locale
    data.sort((a, b) => {
      let aValue = a[this.sortValue];
      let bValue = b[this.sortValue];

      // Conversion en timestamp si ce sont des dates
      if (aValue instanceof Date && bValue instanceof Date) {
        aValue = aValue.getTime();
        bValue = bValue.getTime();
      }

      // Tri numérique
      if (typeof aValue === 'number' && typeof bValue === 'number') {
        return this.sortDirection ? aValue - bValue : bValue - aValue;
      }

      // Tri en tant que chaînes de caractères
      aValue = aValue ? aValue.toString().toLowerCase() : '';
      bValue = bValue ? bValue.toString().toLowerCase() : '';

      if (aValue < bValue) {
        return this.sortDirection ? -1 : 1;
      } else if (aValue > bValue) {
        return this.sortDirection ? 1 : -1;
      }
      return 0;
    });
    this.dataSource.data = data;
  }

  /**
   * Action lors du clic sur une ligne du tableau.
   */
  onRowClicked(row: SuivitConditionDto): void {
    console.log('Ligne cliquée : ', row);
    // Vous pouvez ajouter une redirection ou afficher un modal avec les détails par exemple
  }

  /**
   * Retourne une icône selon le statut du produit.
   */
  getIcon(status: string): string {
    // On normalise la chaîne pour rendre la comparaison insensible à la casse.
    const lowerStatus = status.toLowerCase();

    // Vérification du cas "échue sur traite" en premier (plus spécifique)
    if (lowerStatus.includes('echue sur traite')) return 'bi bi-hourglass-split';
    // Ensuite, le cas "échue"
    if (lowerStatus.includes('echue')) return 'bi bi-exclamation-circle';

    // Autres cas déjà existants
    if (lowerStatus.includes('en cours')) return 'bi bi-arrow-repeat';
    if (lowerStatus.includes('suspendu')) return 'bi bi-exclamation-triangle';
    if (lowerStatus.includes('valide')) return 'bi bi-check-circle';
    if (lowerStatus.includes('en attente')) return 'bi bi-clock';
    if (lowerStatus.includes('clôturé')) return 'bi bi-lock';

    // Valeur par défaut
    return 'bi bi-info-circle';
  }

  /**
   * Retourne une couleur de background selon le statut.
   */
  getCardColor(status: string): string {
    const lowerStatus = status.toLowerCase();

    // Vérification du cas "échue sur traite" en premier
    if (lowerStatus.includes('echue sur traite')) return 'bg-dark text-white';
    // Ensuite, le cas "échue"
    if (lowerStatus.includes('echue')) return 'bg-danger text-white';

    // Les autres cas préexistants
    if (lowerStatus.includes('en cours')) return 'bg-warning text-dark';
    if (lowerStatus.includes('suspendu')) return 'bg-danger text-white';
    if (lowerStatus.includes('valide')) return 'bg-success text-white';
    if (lowerStatus.includes('en attente')) return 'bg-primary text-white';
    if (lowerStatus.includes('clôturé')) return 'bg-secondary text-white';

    // Valeur par défaut
    return 'bg-light text-dark';
  }


  /**
   * Affiche ou masque le menu contextuel de sélection des colonnes.
   */
  toggleContextMenu(): void {
    this.showColumnContextMenu = !this.showColumnContextMenu;
  }

  /**
   * Basculer l'affichage d'une colonne dans le tableau.
   */
  toggleColumn(column: string): void {
    if (this.displayColumns.includes(column)) {
      // Si la colonne est déjà affichée, on la retire
      this.displayColumns = this.displayColumns.filter(col => col !== column);
    } else {
      // Réassemble displayColumns en se basant sur l'ordre des colonnes d'origine (this.columns)
      const orderedColumns = this.columns.map(col => col.field);
      this.displayColumns = orderedColumns.filter(field =>
        this.displayColumns.includes(field) || field === column
      );
    }
  }



  /**
   * Ferme le menu contextuel quand on clique en dehors.
   */
  @HostListener('document:click', ['$event'])
  closeContextMenu(event: Event) {
    const targetElement = event.target as HTMLElement;
    if (!targetElement.closest('.custom-context-menu') && !targetElement.closest('#btn-columns')) {
      this.showColumnContextMenu = false;
    }
  }
   getActionLabel(action: string): string {
    if (action === 'INITIALISATION') {
        return 'Initialisation';
    } else if (action === 'SOUMETTRE_CORDINATOR') {
        return 'SOUMETTRE_CORDINATOR';
    } else if (action === 'SOUMETTRE_LEAD') {
        return 'Soumettre au Lead';
    } else if (action === 'SOUMETTRE_SPONSOR') {
        return 'Soumettre au Sponsor';
    } else if (action === 'EN_COURS_DE_QUALIFICATION_COOR') {
        return 'En cours de qualification COOR';
    } else if (action === 'EN_ATTENTE_DU_CLIENT') {
        return 'En attente du client';
    } else if (action === 'EN_COURS_DE_TRAITEMENT') {
        return 'En cours de traitement';
    } else {
        return action;
    }
}
}
  <div class="table-container mt-1">
  <table mat-table [dataSource]="dataSource" matSort class="mat-elevation-z8" style="width: 100%;">
    <caption style="display: none;">Details about the condition, including its local and criticality information.</caption>

    <!-- Création dynamique des colonnes -->
    <ng-container *ngFor="let column of columns" [matColumnDef]="column.field">
      <th mat-header-cell
          *matHeaderCellDef
          mat-sort-header
          (click)="sortBy(column.field)"
          style="padding: 8px;">
        <!-- Conteneur pour le libellé + filtre -->
        <div class="header-container" style="display: flex; flex-direction: column;">
          <!-- Ligne du titre et indication de tri -->
          <div class="header-label" style="display: flex; align-items: center; justify-content: space-between;">
            <span>{{ column.label }}</span>
       <!--     <span *ngIf="sortValue === column.field">
              <mat-icon *ngIf="sortDirection">arrow_upward</mat-icon>
              <mat-icon *ngIf="!sortDirection">arrow_downward</mat-icon>
            </span>-->
          </div>
          <!-- Ligne du filtre par colonne -->
          <div class="header-filter">
            <input type="text"
                   placeholder="Filtrer {{ column.label }}"
                   (keyup)="applyFilter($event)"
                   name="{{ column.field }}"
                   (click)="$event.stopPropagation()"
                   style="width: 100%; margin-top: 4px; padding: 4px;">
          </div>
        </div>
      </th>
      <!-- <td mat-cell *matCellDef="let element" style="padding: 8px;">
        {{ element[column.field] }}
      </td> -->
      <td mat-cell *matCellDef="let element" style="padding: 8px;">
  {{
    column.field === 'statusProduit'
      ? getActionLabel(element[column.field])
      : element[column.field]
  }}
</td>

    </ng-container>

    <!-- Ligne d'en-tête (les cellules sont créées dynamiquement via le *ngFor ci-dessus) -->
    <tr mat-header-row *matHeaderRowDef="displayColumns"></tr>
    <!-- Ligne de données -->
    <tr mat-row *matRowDef="let row; columns: displayColumns;" (click)="onRowClicked(row)"></tr>
  </table>
  </div>
