  @DeleteMapping("update-attachement/{requestId}/{attachmentId}")
    public void updateAttachement(@PathVariable(name = "requestId") Long requestId,
                                  @PathVariable(name = "attachmentId") Long attachmentId,
                                  @RequestParam(required = false) MultipartFile file) {
        contratService.updateAttachments(requestId,attachmentId,file);
    }
    @Override
    @SneakyThrows
    public void updateAttachments(Long requestId, Long attachmentId, MultipartFile file) {
        contratRepository.findById(requestId).orElseThrow(() -> new RuntimeException("Dossier avec l'id " + requestId + " est introuvables !!!"));
        Attachement attachement = attachementRepository.findById(requestId).orElseThrow(() -> new RuntimeException("fichier avec l'id " + attachmentId + " est introuvables !!!"));
        attachement.setBase64(multiPartFileToBase64(file));
        attachement.setOriginalFileName(file.getOriginalFilename());
        attachement.setDateInjection(LocalDateTime.now());
        attachement.setInjectePar(userService.getAuthenticatedUser().getUserName());
        attachementRepository.save(attachement);
  <tbody>
        <tr class="text-truncate" *ngFor="let item of attachement">
          <td class="px-4 ">{{item?.originalFileName}}</td>
          <td class="px-4 text-uppercase">{{item.dateInjection ||'dd-MM-yyyy'}} </td>
          <td class="px-4 text-capitalize">{{item.injectePar}}</td>
          <td class="editIcon text-center" style="cursor: pointer">
             <nb-icon icon="edit-outline" style="color:#111010" class="m-1" (click)="updateFile()"></nb-icon> 
             <nb-icon icon="download" style="color:#111010" class="m-1" (click)="downLoadFile()"></nb-icon> 
          </td>
        </tr>
        </tbody>
export class ContratAttachementComponent implements OnInit {
  @Input() requestId!:number | undefined;
  @Input() attachement!:Attachement[]
 
  attachementCardBody:boolean=true
  
  constructor(private utilsService: UtilsService, private gestionContratService: GestionContratService){}
  ngOnInit(): void {
  }   

  hideShowAttachementCardBody(){this.attachementCardBody=!this.attachementCardBody}
  onFileSelected(event: any){}

  openFileSelector(){}
  downLoadFile(){}
  updateFile(){
    
  }

} i want the update from the front of attachement
