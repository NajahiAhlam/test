
    initForm(risque?: RisqueInstance) {
      this.form = this.formBuilder.group({
        validation: ['', Validators.required],
        comment: [''],
          conditions: this.formBuilder.array(
          risque?.conditions?.length
            ? risque.conditions.map(c => this.createConditionGroup(c))
            : []
        ),

      });
    }
      getFormControl(control: any): FormControl {
          return control as FormControl;
        }

      createConditionGroup(condition?: Condition): FormGroup {
            return this.formBuilder.group({
              id: [condition ? condition.id : null],
              detail: [condition ? condition.detail : '', [Validators.required]],
              categorie: [condition ? condition.categorie : '', [Validators.required]],
              assigne: ['' , [Validators.required]],
              numberSemaineApresLancement: [condition ? condition.numberSemaineApresLancement : ''],
              dateEcheance: [condition ? condition.dateEcheance :'' ],
              commentCond: ['', [Validators.required]],
            });
          }


          get conditions(): FormArray {
            return this.form.get('conditions') as FormArray;

          }
       
     addCondition(): void {
  const conditionGroup = this.createConditionGroup();
  this.conditions.push(conditionGroup);

  const index = this.conditions.length - 1;

  // Create a new BehaviorSubject for this condition
  const assigneFilter$ = new BehaviorSubject<any[]>(this.user);
  console.log("assigneFilter$: ",assigneFilter$)
  this.filteredAssigneOptions[index] = assigneFilter$;

  // Subscribe to changes in the assigne input
  conditionGroup.get('assigne')?.valueChanges
    .pipe(startWith(''))
    .subscribe(value => {
      const filtered = this.filterOptions(value || '', this.user, 'email');
      assigneFilter$.next(filtered);
    });
}     
        
          removeConditions(index: number): void {
            this.conditions.removeAt(index);
          }
        
