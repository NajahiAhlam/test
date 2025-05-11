  ngOnInit(): void {
    this.wkfService.getRisque().subscribe({
      next:(data:any)=>{
        this.risques = data;
        console.log("risque in ng on init::",this.risques)
        //this._utilService.displaySucess("Commentaire ajouté avec succes","MyCnp")
     
        this.initFormGroup(this.risques)
      }
    })
 this.initFormGroup(this.risques)
    this.userService.getUsersList().subscribe(users => {
      this.user = users.filter(el => el.enabled === true);
      const conditionGroup = this.createConditionGroup();
      if (conditionGroup && conditionGroup.get('assigne')) {
        this.filteredAssigneOptions$ = conditionGroup.get('assigne')!.valueChanges.pipe(
          startWith(''),
          map(filterString => this.filterOptions(filterString, this.user, 'email'))
        );
        console.log(" this.filteredAssigneOptions$: ", this.filteredAssigneOptions$)
      }
    });
    this.userService.getUsersList().subscribe(users => {
      console.log("Users:", users);

      this.user = users;
      console.log("Filtered Users:", this.user);
    });
    
    

   
  }
    <div  class="d-flex justify-content-around my-2 mx-auto p-2">
                        <div class="w-50">
                          <div class="col-form-label my-auto mx-auto">Assigné : <span class="text-danger">*</span></div>
                        </div>
                        <div class="w-50 position-relative">
                          <input  [nbAutocomplete]="autoAssigne" nbInput fullWidth
                                placeholder="Veuillez sélectionner Utilisateur" autocomplete="off"
                                name="assigne" formControlName="assigne">
                          <nb-autocomplete #autoAssigne>
                            <nb-option *ngFor="let user of filteredAssigneOptions$ | async" [value]="user.email">
                              {{ user.email }}
                            </nb-option>
                          </nb-autocomplete>
                        </div>
                      </div>
                       user: any[] = [];
  filteredAssigneOptions$!: Observable<any[]>;
  the autocomplete deosnt work for me when i try to enter on lettre desont filter by it
