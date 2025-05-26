<nb-card style="width: 900px; height: 600px;">
  <nb-card-header class="d-flex justify-content-between">
    <div>Commentaires</div>
    <button nbButton (click)="closeWindow()"><nb-icon class="modal-icon text-dark" icon="xmark" pack="fas"></nb-icon></button>
  </nb-card-header>
    <nb-card-body  style="width: 900px">

      <div class="comments-container">
        <!-- Check if there are comments -->
        <div *ngIf="data" class="comments-list">

          <div class="d-flex flex-row justify-content-start" *ngFor="let item of conditionComment ">
            <div class="m-1">
              <nb-icon icon="user" pack="fas"></nb-icon>
            </div>
            <div class="d-flex flex-row justify-content-between overflow-hidden bg-light m-1 w-100 comment">
              <div class="mx-3">
                <p class="fw-bold m-1">{{item.lastName}} {{item.firstName}}</p>
                <p class="m-1">Posté : {{item.createdAt |  date:"dd/MM/yyyy HH:mm"}}</p>
                <p class="m-1" style="white-space: pre-wrap;">{{item.commentaire}}</p>
              </div>
            </div>
          </div>
        </div>
        <!-- Message if there are no comments -->
        <div *ngIf="!conditionComment || conditionComment?.length! === 0" class="no-comments d-flex flex-column" style="margin-top: 100px; align-items: center;">
          <nb-icon icon="chat-off-fill" style="color: rgb(65, 62, 62); font-size: 30px;" class="m-2"></nb-icon>
          <p class="text-center">Il n'y a pas de commentaires. Merci d'ajouter un commentaire.</p>
        </div>        
      </div>
      <hr>
      <ng-template [ngIf]="_authorizationService.isAssignConditionComment(data?.assigne!, validateur!,lead!)" >
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
        <button type="submit" class="btn btn-dark " (click)="submitComment()" style="float: right;margin-top: 10px;">
          Envoyer
        </button>
      </div>
</ng-template>
    </nb-card-body>
</nb-card>
export class CoditionCommentComponent {
   @Input() data?:ConditionsDTO;
   @Input() validateur ?:string;
   @Input() lead!: string;
   conditionComment!: CommentCondition[];
   
   comment: string = '';


     constructor(private dialogRef:NbDialogRef<CoditionCommentComponent>,private risqueService:GestionRisqueService,
       private _utilService:UtilsService,
       private gestionUtilisateurService: UserService,
       public _authorizationService: AuthorisationService,private cd: ChangeDetectorRef) { }
    ngOnInit(){
      this.fetchData()
    }
      
    fetchData() {
      this.risqueService.getCommentByConditionId(this.data?.id!).subscribe({
        next:(response:any)=>{
        this.conditionComment = response;
        }
      })
    }

    submitComment() {
      if (this.comment.trim().length === 0) {
        this._utilService.displayWarning("Merci de saisir un commentaire valide", "MyCnp");
        return;
      }
      this.risqueService.joinCommentCondition({ requestId: this.data?.id!, comment: this.comment }).subscribe({
        next: (data: any) => {
         
          this._utilService.displaySucess("Commentaire ajouté avec succès", "MyCnp");
          this.fetchData();
          this.comment = '';  
          this.data?.commentCondition.unshift(data);
    
          
       
        }
      });
    }
    
    
  closeWindow(){
    this.dialogRef.close();
  }


}
