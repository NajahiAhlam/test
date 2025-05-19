filteredAssigneOptions: BehaviorSubject<any[]>[] = [];
ngOnInit() {
  this.initForm();

  this.form.get('validation')?.valueChanges.subscribe(value => {
    this.validationValue = value;
    const commentCtrl = this.form.get('comment');
    const conditionsArray = this.form.get('conditions') as FormArray;

    if (value === 'Sans Condition') {
      commentCtrl?.setValidators([Validators.required]);
    } else {
      commentCtrl?.clearValidators();

      // Ensure there's at least one condition
      if (conditionsArray.length === 0) {
        this.addCondition();
      }
    }
    commentCtrl?.updateValueAndValidity();
  });

  this.userService.getUsersList().subscribe(users => {
    this.user = users.filter(el => el.enabled === true);
  });
}
addCondition(): void {
  const conditionGroup = this.createConditionGroup();
  this.conditions.push(conditionGroup);

  const index = this.conditions.length - 1;

  // Create a new BehaviorSubject for this condition
  const assigneFilter$ = new BehaviorSubject<any[]>(this.user);
  this.filteredAssigneOptions[index] = assigneFilter$;

  // Subscribe to changes in the assigne input
  conditionGroup.get('assigne')?.valueChanges
    .pipe(startWith(''))
    .subscribe(value => {
      const filtered = this.filterOptions(value || '', this.user, 'email');
      assigneFilter$.next(filtered);
    });
}
<nb-autocomplete #autoAssigne>
  <nb-option *ngFor="let user of filteredAssigneOptions[i] | async" [value]="user.email">
    {{ user.email }}
  </nb-option>
</nb-autocomplete>
filterOptions(filterString: string, options: any[], field: string): any[] {
  const filterValue = filterString.toLowerCase();
  return options.filter(option => option[field].toLowerCase().includes(filterValue));
}
