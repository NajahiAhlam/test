addCondition(): void {
  const conditionGroup = this.createConditionGroup();
  this.conditions.push(conditionGroup);

  const index = this.conditions.length - 1;

  // Create a filtered observable for this specific assigne field
  conditionGroup.get('assigne')!.valueChanges
    .pipe(
      startWith(''),
      map(value => this.filterOptions(value || '', this.user, 'email'))
    )
    .subscribe(filtered => {
      // Assign filtered list to a per-index property
      this.filteredAssigneOptions[index] = filtered;
    });
}
filteredAssigneOptions: { [index: number]: any[] } = {};
<nb-option *ngFor="let user of filteredAssigneOptions[i]" [value]="user.email">
  {{ user.email }}
</nb-option>
