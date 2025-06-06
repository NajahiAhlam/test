createConditionGroup(condition?: Condition): FormGroup {
  const group = this.formBuilder.group({
    id: [condition?.id ?? null],
    detail: [condition?.detail ?? '', Validators.required],
    categorie: [condition?.categorie ?? '', Validators.required],
    assigne: [condition?.assigne ?? '', Validators.required],
    numberSemaineApresLancement: [condition?.numberSemaineApresLancement ?? ''],
    dateEcheance: [condition?.dateEcheance ?? ''],
    commentCond: [condition?.commentCond ?? '', Validators.required],
  });

  // Set up conditional validators based on `categorie`
  group.get('categorie')?.valueChanges
    .pipe(startWith(group.get('categorie')?.value ?? ''))
    .subscribe((value: string) => {
      const dateCtrl = group.get('dateEcheance');
      const semaineCtrl = group.get('numberSemaineApresLancement');

      if (value === 'poste condition') {
        dateCtrl?.setValidators([Validators.required]);
        semaineCtrl?.clearValidators();
      } else if (value === 'precondition') {
        semaineCtrl?.setValidators([Validators.required]);
        dateCtrl?.clearValidators();
      } else {
        // Default: clear both
        dateCtrl?.clearValidators();
        semaineCtrl?.clearValidators();
      }

      dateCtrl?.updateValueAndValidity({ onlySelf: true });
      semaineCtrl?.updateValueAndValidity({ onlySelf: true });
    });

  return group;
}
