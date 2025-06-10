  data$: ObjectPagination<DemandeQualification> | undefined
  data$$: Observable<AppDataState<ObjectPagination<DemandeQualification>>> | undefined;
  demande: DemandeQualification[] = [];
  errorMessage!: string;
  readonly DataStateEnum = DataStateEnum;
  elementPerPage = 10;
  pageNumber = 0;
  sortDirection = false;
  sortValue: string = "";
  searchFormGroup!: FormGroup;
  loading : boolean = false
  highlightFirstElement!:boolean
  canReadBuffer!:boolean
  @ViewChildren('rowCheckbox') rowCheckboxes!: QueryList<ElementRef>
  selectedRows: DemandeQualification[] = [];
  displayColumns: Array<string>=['Titre','Filiale','Type','Description','Date de Lancement',"Date de création",'Porteur Projet','Type Demande','Statut'];
  subscription!: Subscription;
  private valueChangesSubscription!: Subscription;
  isDropdownOpen = false;
  toggleDropdown(event: Event): void {
    event.stopPropagation(); // Prevent click from propagating and closing the dropdown
    this.isDropdownOpen = !this.isDropdownOpen;
  }
  @HostListener('document:click', ['$event'])
  closeDropdown(event: Event): void {
    // Close the dropdown if clicking outside
    const target = event.target as HTMLElement;
    if (!target.closest('.dropdown')) {
      this.isDropdownOpen = false;
    }
  }
  columns = [
   { key: 'objetDemande', label: 'Titre' },
   { key: 'nomFiliale', label: 'Filiale' },
   { key: 'type', label: 'Type' },
   { key: 'dateCreation', label: 'Date de création' },
   { key: 'description', label: 'Description' },
   { key: 'dateLancement', label: 'Date de Lancement' },
   { key: 'porteurProjet', label: 'Porteur Projet' },
   { key: 'dateModification', label: 'Date de Modification' },
   { key: 'porteurMetier', label: 'Porteur Metier' },
   { key: 'status', label: 'Statut' },
   { key: 'dateKickOff', label: 'Date de KickOff' },
   { key: 'kickOffDecision', label: 'Decision kickOff' },
    { key: 'typeDemande', label: 'Type Demande' },
   
 ];
 showColumnContextMenu: boolean=false;
 constructor(private _WkfService: WkfService,
   private userService: UserService,
   private toastrService: NbToastrService,
   private fb: FormBuilder,
   private utilsService: UtilsService,
   private router: Router,
   private windowService: NbWindowService,
   private route: ActivatedRoute,
   private renderer: Renderer2,
   private el: ElementRef,private htmlExportService: ExportHtmlService,
  private exportCSVService: ExportCSVService){}


 ngOnInit(): void {
 
   this.initSearchForm();
 
   // Use setTimeout to delay non-essential operations (e.g., local storage management)
   setTimeout(() => {
     localStorage.removeItem("columns");
     const storedColumns = localStorage.getItem("columns");
     if (storedColumns) {
       this.displayColumns = JSON.parse(storedColumns);
     } else {
       localStorage.setItem("columns", JSON.stringify(this.displayColumns));
     }
   }, 0);
 
   // Fetch projects asynchronously
   setTimeout(() => {
     this.findAllRequests();
   }, 100);
 }
 
 private findAllRequests() {
   this.loading = true;
   this._WkfService.findAllRequests(this.pageNumber, this.elementPerPage, this.sortValue, this.sortDirection ? 'ASC' : 'DESC', this.searchFormGroup.value).subscribe({
       next: (data) => {
           this.data$ = data;
          
           this.loading = false;
       },
       error: (err) => {
           this.loading = false;
           this.utilsService.displayError('Une erreur technique est survenue', "Erreur");
       }
   });
}
