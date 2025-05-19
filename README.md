this.userService.getUsersList().subscribe(sponsors => {
  this.user = sponsors.filter(el => el.enabled === true);
  
  // Add filtering for existing condition inputs
  this.conditions.controls.forEach((group, index) => {
    group.get('assigne')!.valueChanges
      .pipe(
        startWith(''),
        map(value => this.filterOptions(value || '', this.user, 'email'))
      )
      .subscribe(filtered => {
        this.filteredAssigneOptions[index] = filtered;
      });
  });
});
