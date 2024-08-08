  uploadFile(file: File): Observable<any> {
    const formData: FormData = new FormData();
    formData.append('file', file, file.name);

    return this._http.post(this.uploadUrl, formData, {
        reportProgress: true,
        responseType: 'json'
    }).pipe(
        catchError(error => {
            console.error('Error uploading file:', error);
            return throwError(error);
        })
    );
    
    <div class="mx-3">
      <input type="file" accept=".xls,.xlsx,.csv" style="display: none" (change)="uploadFile($event)" #fileInput />
      <button *ngIf="canUpload" class="float-right" nbButton status="primary" (click)="fileInput.click()" >
        déposer Fichier
      </button>
    </div>
      onFileSelected(fileInput: HTMLInputElement): void {
    fileInput.click();
  }
  
  uploadFile(event: Event): void {
    const target = event.target as HTMLInputElement;
    const files = target.files;
  
    if (files) {
      this.progress = 0;
      this.selectedFile = files[0];
  
      if (this.selectedFile) {
        console.log('outside', this.selectedFile)
        this.excelService.uploadFile(this.selectedFile).subscribe({
          next: (data => {
            this._utilsService.displaySucess("piece joint ajoutée avec succès", "WorkFlow Cockpit")
            location.reload
          }),
          error: (err => {
          })
        })
      }
    }
  }
this is an exemple of how my logic work this code before i do one component for all
