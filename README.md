uploadFile(file: File): void {
  const formData = new FormData();
  formData.append('file', file, file.name);

  let uploadObservable: Observable<any> | undefined;

  switch (this.selectedOption) {
    case 'Extract du Suivi des Portefeuilles Projets':
      uploadObservable = this.excelService.uploadFilee(formData);
      break;
    case 'Sciforma Suivi des Timesheets SGMA':
      // Assign the appropriate service method for this case
      // uploadObservable = this.sciformaService.uploadFile(formData);
      break;
    case 'P2P du Suivi des Engagements':
      // Assign the appropriate service method for this case
      // uploadObservable = this.p2pService.uploadFile(formData);
      break;
    default:
      console.error('Unknown process selected');
      return; // Exit if no valid option is selected
  }

  if (uploadObservable) {
    uploadObservable.pipe(
      catchError(error => {
        console.error('Error uploading file:', error);
        return throwError(error);
      })
    ).subscribe({
      next: (data) => {
        this.utilsService.displaySucess("Piece jointe ajoutée avec succès", "WorkFlow Cockpit");
        this.resetUpload();
      },
      error: (err) => {
        console.error('Upload error:', err);
      }
    });
  } else {
    console.error('No upload observable available');
  }
}
