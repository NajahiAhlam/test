getEligibilityQuestions(): any[] {
  return this.questionList.filter(q => 
    !q.numQuestion.includes('.') &&
    parseInt(q.numQuestion) >= 1 && parseInt(q.numQuestion) <= 5
  );
}

getCNPQuestions(): any[] {
  return this.showAdditionalContent
    ? this.questionList.filter(q => 
        !q.numQuestion.includes('.') &&
        parseInt(q.numQuestion) >= 6 && parseInt(q.numQuestion) <= 15
      )
    : [];
}
Can't bind to 'ngForOf' since it isn't a known property of 'ng-container'.
1. If 'ngForOf' is an Angular directive, then add 'CommonModule' to the '@NgModule.imports' of this component.
2. To allow any property add 'NO_ERRORS_SCHEMA' to the '@NgModule.schemas' of this component.ngtsc(-998002)
start-request-form.component.ts(15, 18): Error occurs in the template of component StartRequestFormComponent.
No quick fixes available
Property 'numQuestion' does not exist on type 'Observable<Question[]>'.ngtsc(2339)
start-request-form.component.ts(15, 18): Error occurs in the template of component StartRequestFormComponent.
Property 'question' does not exist on type 'Observable<Question[]>'.ngtsc(2339)
 <tr *ngIf="showAdditionalContent" class="table-section-header">
      <td colspan="3"><strong>Question CNP</strong></td>
    </tr>
    <ng-container *ngIf="showAdditionalContent" [ngForOf]="getCNPQuestions()" let-question>
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
       
