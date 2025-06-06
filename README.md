<input
  type="text"
  class="form-control"
  [formControl]="conditionGroup.get('numberSemaineApresLancement')"
/>
<div *ngIf="conditionGroup.get('numberSemaineApresLancement')?.hasError('required')">
  Ce champ est requis
</div>
createConditionGroup(condition?: Condition): FormGroup {
  const group = this.formBuilder.group({
    id: [condition ? condition.id : null],
    detail: [condition ? condition.detail : '', [Validators.required]],
    categorie: [condition ? condition.categorie : '', [Validators.required]],
    assigne: ['', [Validators.required]],
    numberSemaineApresLancement: [condition ? condition.numberSemaineApresLancement : ''],
    dateEcheance: [condition ? condition.dateEcheance : ''],
    commentCond: ['', [Validators.required]],
  });

  // Dynamically apply validators based on categorie value
  group.get('categorie')?.valueChanges
    .pipe(startWith(group.get('categorie')?.value ?? ''))
    .subscribe((value: string | null) => {
      const dateCtrl = group.get('dateEcheance');
      const semaineCtrl = group.get('numberSemaineApresLancement');

      if (value === 'poste condition') {
        dateCtrl?.setValidators([Validators.required]);
        semaineCtrl?.clearValidators();
      } else if (value === 'precondition') {
        semaineCtrl?.setValidators([Validators.required]);
        dateCtrl?.clearValidators();
      } else {
        dateCtrl?.clearValidators();
        semaineCtrl?.clearValidators();
      }

      dateCtrl?.updateValueAndValidity();
      semaineCtrl?.updateValueAndValidity();
    });

  return group;
}
