<tbody>
  <ng-container *ngFor="let question of getFilteredParentQuestions(); let i = index">
    
    <!-- Insert CNP label row before question 6 -->
    <tr *ngIf="parseInt(question.numQuestion) === 6">
      <td colspan="3" style="font-weight: bold; background-color: #f0f0f0;">
        Section CNP – Merci de répondre aux questions suivantes
      </td>
    </tr>

    <!-- Normal question row -->
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

    <!-- Sub-questions -->
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
