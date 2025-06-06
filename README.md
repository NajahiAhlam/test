initFormGroup(risque?: Risque) {
  this.formGroup = this.formBuilder.group({
    risqueId: [risque?.id ?? ''],
    contexte: [risque?.contexte ?? '', Validators.required],
    niveauIntrinseque: [risque?.niveauIntrinseque ?? '', Validators.required],
    autreRisque: [''], // <- Optional field
    niveauRisqueResiduel: [risque?.niveauRisqueResiduel ?? '', Validators.required],
    comment: [risque?.comment ?? '', Validators.required],
    zoneRisques: this.formBuilder.array(
      risque?.zoneRisques?.length
        ? risque.zoneRisques.map(zone => this.createZoneGroup(zone))
        : []
    )
  });
}
createAnalyseRisqueGroup(analyseRisque: AnalyseRisque) {
  return this.formBuilder.group({
    analyseRisqueId: [analyseRisque.id],
    description: [analyseRisque.description || '', Validators.required],
    reponse: [analyseRisque.reponse || '', Validators.required],
    facteur: [analyseRisque.facteur || '', Validators.required],
  });
}
onSubmit() {
  if (!this.formGroup.valid) {
    this.formGroup.markAllAsTouched(); // Mark all fields to show errors
    this._utilService.displayWarning("Tous les champs obligatoires doivent être remplis", "Attention");
    return;
  }

  console.log("form value: ", this.formGroup.value);
  this.wkfService.updateRisque(this.demandetId!, this.formGroup.value).subscribe({
    next: () => {
      this._utilService.displaySucess("Risque ajouté avec succès", "Succès");
      this.dialogRef.close("SUCCESS");
    },
    error: (err) => {
      this._utilService.displayError(err.error.message, "Erreur");
    },
  });
}
