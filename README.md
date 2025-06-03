     <nb-tab tabTitle="Questionnaire" style="width: 780px; height: 600px;">
               <nb-alert *ngIf="showMesssageEligibe && closeAlert" status="success" closable (click)="closeAlerte()">
              Eligile, Il s'agit d'un Nouveau Produit au sens de l'instruction SG. 
              Un CNP est requis pour valider le lancement de ce produit. Merci de continuer le questionnaire à partir de la question 6.
            </nb-alert>
                <nb-alert *ngIf="message" status="info" closable (close)="message = ''">
              {{ message }}
            </nb-alert>
              <table aria-label="This table displays the data related to Questionnaire">
                <thead>
                  <tr>
                    <th>Num Question</th>
                    <th>Question</th>
                    <th>Response</th>
                  </tr>
                </thead>
                <tbody>
                  <ng-container *ngFor="let item of getParentQuestions()"> <!-- Parent question -->
                    <tr>
                      <td>
                        <!-- Show expand/collapse icon only if parent has sub-questions -->
                        <span *ngIf="hasSubQuestions(item.parentQuestion.numQuestion)"
                              (click)="toggleSubQuestions(item.parentQuestion.numQuestion)"
                              style="cursor: pointer;">
                          <nb-icon [icon]="expandedParents[item.parentQuestion.numQuestion] ? 'chevron-up' : 'chevron-down'"></nb-icon>
                        </span>
                        {{ item.parentQuestion.numQuestion }}
                      </td>
                      <td>{{ item.parentQuestion.question }}</td>
                      <!-- Hide checkbox for parent questions with sub-questions -->
                      <td *ngIf="!hasSubQuestions(item.parentQuestion.numQuestion)">
                        <input type="checkbox" [checked]="item.answered" (change)="updateAnswer(item, $event)">
                      </td>
                    </tr>
            
                    <!-- Show sub-question under its parent -->
                    <ng-container *ngIf="expandedParents[item.parentQuestion.numQuestion]">
                      <tr *ngFor="let subItem of getSubQuestions(item.parentQuestion.numQuestion)">
                        <td>&nbsp;&nbsp;{{ subItem.subQuestion.numQuestion }}</td> <!-- Indent for clarity -->
                        <td>{{ subItem.subQuestion.question }}</td>
                        <td>
                          <input type="checkbox" [checked]="subItem.answered" (change)="updateAnswer(subItem, $event)">
                        </td>
                      </tr>
                    </ng-container>
                  </ng-container>
                </tbody>
              </table>
            </nb-tab>
             ngOnInit() {
      console.log("demandeaaa: ",this.demande)
      this.initFormGroup(this.demande);
      this._wkfService.getAll().subscribe((data: any[]) => {
        this.questionList = data.map(q => ({ ...q, response: false })); 
      });}

        expandedParents: { [key: string]: boolean } = {}; // Store expand/collapse state

    toggleSubQuestions(parentNum: any) {
      if (this.expandedParents[parentNum] === undefined) {
        this.expandedParents[parentNum] = true;
      } else {
        this.expandedParents[parentNum] = !this.expandedParents[parentNum];
      }
      this.cdr.detectChanges();
    }

    getParentQuestionsAllData(): any[] {
      console.log("isRequiredQuestionsConditionMet",this.isRequiredQuestionsConditionMet())
      return this.demande?.demandeQualificationQuestionReponses
        ?.filter((q, index, self) => 
          q.parentQuestion && 
          self.findIndex(p => p.parentQuestion.numQuestion === q.parentQuestion.numQuestion) === index
        )
        .sort((a, b) => parseFloat(a.parentQuestion.numQuestion.toString()) - parseFloat(b.parentQuestion.numQuestion.toString())) || [];
    }
    getParentQuestionsJustFiveFirst(): any[] {
      console.log("isRequiredQuestionsConditionMet", this.isRequiredQuestionsConditionMet());
      return this.demande?.demandeQualificationQuestionReponses
        ?.filter((q, index, self) =>
          q.parentQuestion &&
          self.findIndex(p => p.parentQuestion.numQuestion === q.parentQuestion.numQuestion) === index &&
          q.parentQuestion.numQuestion >= 1 && q.parentQuestion.numQuestion <= 5 
        )
        .sort((a, b) => parseFloat(a.parentQuestion.numQuestion.toString()) - parseFloat(b.parentQuestion.numQuestion.toString())) || [];
    }
    getParentQuestions(): any[] {
    
      return this.demande?.demandeQualificationQuestionReponses
        ?.filter((q, index, self) =>
          q.parentQuestion &&
          self.findIndex(p => p.parentQuestion.numQuestion === q.parentQuestion.numQuestion) === index &&
          (this.isRequiredQuestionsConditionMet() ? true : (q.parentQuestion.numQuestion >= 1 && q.parentQuestion.numQuestion <= 5)) 
        )
        .sort((a, b) => parseFloat(a.parentQuestion.numQuestion.toString()) - parseFloat(b.parentQuestion.numQuestion.toString())) || [];
    }


    isRequiredQuestionsConditionMet(): boolean {
      const responses = this.demande?.demandeQualificationQuestionReponses || [];
      
      const question1 = responses.find(q => q.parentQuestion?.numQuestion.toString() === '1');
      if (!question1 || !question1.answered) return false;
      return responses.some(q => 
        ['2', '3', '4', '5'].includes(q.parentQuestion?.numQuestion.toString()) && q.answered
      ) || this.getSubQuestions('3').some(subQ => subQ.answered);
    }
    
    getSubQuestions(parentNum: string): any[] {
      const subQuestions = this.demande?.demandeQualificationQuestionReponses
        ?.filter(q => q.subQuestion && q.parentQuestion.numQuestion.toString() === parentNum.toString())
        .sort((a, b) => parseFloat(a.subQuestion.numQuestion.toString()) - parseFloat(b.subQuestion.numQuestion.toString())) || [];
    
      console.log(`Sorted Sub-questions for parent ${parentNum}:`, subQuestions);
      return subQuestions;
    }
    
    
    
    
    hasSubQuestions(parentNum: any): boolean {
      return this.demande?.demandeQualificationQuestionReponses?.some(
        q => q.subQuestion && q.parentQuestion.numQuestion.toString() === parentNum.toString()
      ) ?? false;
    }
    this is the update form i want to do same logic if there is just one question checked or more from 1 to 5 inclue the subQuestion to show just from question 1 to five and display other if question num 1 and at least other question from 2 to 5 inclue the subQuestion of that question to display other and show nb-Alert 
