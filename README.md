   <nb-tab tabTitle="Questionnaire" style="width: 780px; height: 600px;">
            <nb-alert *ngIf="showAdditionalContent && closeAlert" status="success" closable (click)="closeAlerte()">
              Eligile, Il s'agit d'un Nouveau Produit au sens de l'instruction SG. 
              Un CNP est requis pour valider le lancement de ce produit. Merci de continuer le questionnaire à partir de la question 6.
            </nb-alert>
            <nb-alert *ngIf="message" status="info" closable (close)="message = ''">
              {{ message }}
            </nb-alert>

            <table class="table" aria-label="This table displays the data related to Questionnaire">
              <thead>
                <tr>
                  <th>Num Question</th>
                  <th>Question</th>
                  <th>Réponse</th>
                </tr>
              </thead>
              <tbody>
                <ng-container *ngFor="let question of getFilteredParentQuestions()">
                  <tr>
                    <td>
                      <!-- Show icon only if the question has sub-questions -->
                      <span *ngIf="hasSubQuestions(question.numQuestion)"
                            (click)="toggleSubQuestions(question.numQuestion)"
                            style="cursor: pointer;">
                        <nb-icon [icon]="expandedParents[question.numQuestion] ? 'chevron-up' : 'chevron-down'"></nb-icon>
                      </span>
                      {{ question.numQuestion }}
                    </td>
                    <td>{{ question.question }}</td>
                    <!-- Hide checkbox for parents -->
                    <td *ngIf="!hasSubQuestions(question.numQuestion)">
                      <label class="switch">
                        <input type="checkbox" [checked]="question.response" (change)="toggleResponse(question)"> 
                      
                        
                        
                        <span class="slider round"></span>
                      </label>
                    </td>
                  </tr>
          
                  <!-- Sub-questions (only show if expanded) -->
                  <ng-container *ngIf="expandedParents[question.numQuestion]">
                    <tr *ngFor="let subQuestion of getSubQuestions(question.numQuestion)">
                      <td>&nbsp;&nbsp;{{ subQuestion.numQuestion }}</td> <!-- Indent for clarity -->
                      <td>{{ subQuestion.question }}</td>
                      <td>
                        <label class="switch">
                           <input type="checkbox" [checked]="subQuestion.response" (change)="toggleResponse(subQuestion)"> 
                         
                          <span class="slider round"></span>
                        </label>
                      </td>
                    </tr>
                  </ng-container>
                </ng-container>
              </tbody>
            </table>
          </nb-tab>
  ngOnInit() {
    this.initFormGroup();
    this._wkfService.getAll().subscribe((data: any[]) => {
      this.questionList = data.map(q => ({ ...q, response: false }));
    });}
   hasSubQuestions(num: string): boolean {
      return this.questionList.some(q => q.numQuestion.startsWith(num + "."));
    }

   getSubQuestions(parentNum: string): any[] {
      return this.questionList
        .filter(q => q.numQuestion.startsWith(parentNum + "."))
        .sort((a, b) => parseFloat(a.numQuestion) - parseFloat(b.numQuestion)); // Sort numerically
    }



    toggleSubQuestions(parentNum: any) {
      this.expandedParents[parentNum] = !this.expandedParents[parentNum];
    }

  getFilteredParentQuestions(): any[] {
      return this.showAdditionalContent
        ? this.questionList.filter(q => !q.numQuestion.includes('.') && parseInt(q.numQuestion) >= 1)
        : this.questionList.filter(q => !q.numQuestion.includes('.') && parseInt(q.numQuestion) >= 1 && parseInt(q.numQuestion) <= 5);
    }

     toggleResponse(question: any): void {
    question.response = !question.response;

    if (question.response) {
      this.checkedQuestions.add(question.numQuestion);
    } else {
      this.checkedQuestions.delete(question.numQuestion);
    }

    this.checkPopupCondition();
    setTimeout(() => this.cdr.detectChanges(), 0);
  }
  checkPopupCondition(): void {
    if (this.checkedQuestions.has("1") && this.checkedQuestions.size > 1) {
     // alert("You have selected question 1 and another question!"); // Show popup
      this.showAdditionalContent = true; // Display additional content

    //  this.utilsService.displaySucess("Il s'agit d'un Nouveau Produit au sens de l'instruction SG. Un CNP est requis pour valider le lancement de ce produit. Merci de continuer le questionnaire.","Eligible")
 
    }
  }
  getExtraQuestions(): any[] {
    return this.questionList.filter(q => parseInt(q.numQuestion) > 5);
  }
