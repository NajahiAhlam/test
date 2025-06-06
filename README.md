 <nb-tab tabTitle="Questionnaire" style="width: 780px; height: 600px;">
            <nb-alert *ngIf="showAdditionalContent && closeAlert" status="success"  >
              Éligible, Il s'agit d'un Nouveau Produit au sens de l'instruction SG. 
              Un CNP est requis pour valider le lancement de ce produit. Merci de continuer le questionnaire à partir de la question 6.
            </nb-alert>
             <nb-alert *ngIf="!showAdditionalContent && closeAlert" status="danger"  >
              Le questionnaire n'est pas éligible.
            </nb-alert>
            <nb-alert *ngIf="message && showAdditionalContent" status="info"  (close)="message = ''">
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
                      <span *ngIf="hasSubQuestions(question.numQuestion)"
                            (click)="toggleSubQuestions(question.numQuestion)"
                            style="cursor: pointer;">
                        <nb-icon [icon]="expandedParents[question.numQuestion] ? 'chevron-up' : 'chevron-down'"></nb-icon>
                      </span>
                      {{ question.numQuestion }}
                    </td>
                    <td>{{ question.question }}</td>
                    <td *ngIf="!hasSubQuestions(question.numQuestion)">
                      <label class="switch">
                        <input type="checkbox" [checked]="question.response" (change)="toggleResponse(question)"> 
                      
                        
                        
                        <span class="slider round"></span>
                      </label>
                    </td>
                  </tr>
          
                  <ng-container *ngIf="expandedParents[question.numQuestion]">
                    <tr *ngFor="let subQuestion of getSubQuestions(question.numQuestion)">
                      <td>&nbsp;&nbsp;{{ subQuestion.numQuestion }}</td> 
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
    checkPopupCondition(): void {
  const hasQ1 = Array.from(this.checkedQuestions).some(q => q === "1" || q.startsWith("1."));
  const hasAnother = Array.from(this.checkedQuestions).some(q => {
    if (q === "1" || q.startsWith("1.")) return false;
    const num = parseInt(q.split('.')[0]);
    return num >= 1 && num <= 5;
  });

  const newShow = hasQ1 && hasAnother;

  if (newShow && !this.showAdditionalContent) {
    // condition just became true → show alert again
    this.closeAlert = true; // reopen the alert
  }

  this.showAdditionalContent = newShow;

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

  getExtraQuestions(): any[] {
    return this.questionList.filter(q => parseInt(q.numQuestion) > 5);
  }


  initFormGroup() {
    this.form = this.formBuilder.group({
      filiale: ['', Validators.required],
      type: [''],
      date: [''],
      dateReel: [''],
      porteurProjet: ['', Validators.required],
      porteurMetier: ['', Validators.required],
      sponsor: ['', Validators.required],
      objet: ['', Validators.required],
      description: ['']
    });
  }
hey chat i want to saparate my tables by titre from question num 1 to 5 do titre question eligibilite from question num 6 to 15 do titre question cnp
