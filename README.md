<div class="comment-input-container">
  <textarea
    fullWidth
    class="form-control"
    nbInput
    name="comment"
    required
    placeholder="Laisser un commentaire"
    type="text"
    [(ngModel)]="comment"
    style="resize: none;"
  ></textarea>

  <!-- File input -->
  <input
    type="file"
    (change)="onFileSelected($event)"
    class="form-control mt-2"
    accept=".pdf,.png,.jpg,.jpeg,.doc,.docx"  <!-- Optional: specify file types -->
  />

  <button
    type="submit"
    class="btn btn-dark"
    (click)="submitComment()"
    style="float: right; margin-top: 10px;"
  >
    Envoyer
  </button>
</div>
selectedFile?: File | null = null;

onFileSelected(event: Event) {
  const fileInput = event.target as HTMLInputElement;
  if (fileInput.files && fileInput.files.length > 0) {
    this.selectedFile = fileInput.files[0];
  }
}

submitComment() {
  if (this.comment.trim().length === 0) {
    this._utilService.displayWarning("Merci de saisir un commentaire valide", "MyCnp");
    return;
  }

  const formData = new FormData();
  formData.append('requestId', this.data?.id!.toString());
  formData.append('comment', this.comment);

  if (this.selectedFile) {
    formData.append('file', this.selectedFile);
  }

  this.risqueService.joinCommentConditionWithFile(formData).subscribe({
    next: (data: any) => {
      this._utilService.displaySucess("Commentaire ajouté avec succès", "MyCnp");
      this.fetchData();
      this.comment = '';
      this.selectedFile = null;
      if (this.data?.commentCondition) {
        this.data.commentCondition.unshift(data);
      }
    }
  });
}
