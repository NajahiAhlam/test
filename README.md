onReset() {
  this.zoneRisquesArray.controls.forEach(zoneGroup => {
    const analyseInstances = zoneGroup.get('analyseInstances') as FormArray;

    analyseInstances.controls.forEach(analyseGroup => {
      analyseGroup.get('reponse')?.reset('');
      analyseGroup.get('facteur')?.reset('');
    });
  });
}
