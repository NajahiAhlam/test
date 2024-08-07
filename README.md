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
}
