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
<table class="table" aria-label="This table displays the data related to Questionnaire">
  <thead>
    <tr>
      <th>Num Question</th>
      <th>Question</th>
      <th>Réponse</th>
    </tr>
  </thead>
  <tbody>

    <!-- Section: Question Éligibilité (1-5) -->
    <tr class="table-section-header">
      <td colspan="3"><strong>Question Éligibilité</strong></td>
    </tr>
    <ng-container *ngFor="let question of getEligibilityQuestions()">
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

    <!-- Section: Question CNP (6-15) -->
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
