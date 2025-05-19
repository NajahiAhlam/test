this.form.get('validation')?.valueChanges.subscribe(value => {
  this.validationValue = value;

  const commentCtrl = this.form.get('comment');
  const conditionsArray = this.form.get('conditions') as FormArray;

  if (value === 'Sans Condition') {
    commentCtrl?.setValidators([Validators.required]);
    commentCtrl?.updateValueAndValidity();
  } else {
    commentCtrl?.clearValidators();
    commentCtrl?.updateValueAndValidity();

    if (conditionsArray.length === 0) {
      this.addCondition();  // 👈 Add one condition by default
    }
  }
});
