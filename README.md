export class CoditionCommentComponent {
  @Input() data?: ConditionsDTO;
  @Input() validateur?: string;
  @Input() lead!: string;

  conditionComment!: CommentCondition[];
  comment: string = '';
  selectedFile?: File | null = null;

  constructor(
    private dialogRef: NbDialogRef<CoditionCommentComponent>,
    private risqueService: GestionRisqueService,
    private _utilService: UtilsService,
    private gestionUtilisateurService: UserService,
    public _authorizationService: AuthorisationService,
    private cd: ChangeDetectorRef
  ) {}

  ngOnInit() {
    this.fetchData();
  }

  fetchData() {
    this.risqueService.getCommentByConditionId(this.data?.id!).subscribe({
      next: (response: any) => {
        this.conditionComment = response;
      },
      error: (err) => {
        this._utilService.displayError("Erreur lors du chargement des commentaires", "MyCnp");
      }
    });
  }

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

    // Step 1: Send comment
    this.risqueService.joinCommentCondition({
      requestId: this.data?.id!,
      comment: this.comment
    }).subscribe({
      next: (commentData: any) => {
        this._utilService.displaySucess("Commentaire ajouté avec succès", "MyCnp");

        // Step 2: If a file is selected, upload it
        if (this.selectedFile) {
          const formData = new FormData();
          formData.append('file', this.selectedFile);
          formData.append('conditionId', this.data?.id!.toString()); // Or commentData.id if needed

          this.risqueService.uploadConditionCommentFile(formData).subscribe({
            next: () => {
              this._utilService.displaySucess("Fichier ajouté avec succès", "MyCnp");
              this.afterSubmitCleanup(commentData);
            },
            error: () => {
              this._utilService.displayError("Erreur lors de l'envoi du fichier", "MyCnp");
              this.afterSubmitCleanup(commentData); // Optional: still update comments
            }
          });
        } else {
          this.afterSubmitCleanup(commentData);
        }
      },
      error: () => {
        this._utilService.displayError("Erreur lors de l'envoi du commentaire", "MyCnp");
      }
    });
  }

  afterSubmitCleanup(commentData: any) {
    this.fetchData();
    this.comment = '';
    this.selectedFile = null;

    if (this.data?.commentCondition) {
      this.data.commentCondition.unshift(commentData);
    }
  }

  closeWindow() {
    this.dialogRef.close();
  }
}
