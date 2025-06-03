<tbody>
  <!-- Section Header: Éligibilité -->
  <tr>
    <td colspan="3" style="font-weight: bold; background-color: #f0f0f0;">
      🟦 Questions Éligibilité (1 à 5)
    </td>
  </tr>

  <ng-container *ngFor="let item of getParentQuestionsJustFiveFirst()">
    <!-- Your parent + sub-question rendering for 1–5 here -->
    <!-- Same structure you already use -->
  </ng-container>

  <!-- Only show CNP section if eligible -->
  <tr *ngIf="isRequiredQuestionsConditionMet()">
    <td colspan="3" style="font-weight: bold; background-color: #f0f0f0;">
      🟨 Questions CNP (6 et plus)
    </td>
  </tr>

  <ng-container *ngIf="isRequiredQuestionsConditionMet()" 
                [ngForOf]="getParentQuestionsAllData().filter(p => p.parentQuestion.numQuestion > 5)" 
                let-item>
    <!-- Render parent + sub-question as usual for 6+ -->
  </ng-container>
</tbody>
