<div class="key-value-pair" *ngIf="data?.peoples">
  <div class="key col-6">Coordinateur:</div>
  <div class="value col-6">
    <ng-container *ngFor="let person of data.peoples; let last = last">
      <span [ngClass]="{ highlight: isCoordinatorHighlight(person) }">
        {{ person.assignName }}{{ !last ? ', ' : '' }}
      </span>
    </ng-container>
  </div>
</div>
.highlight {
  background-color: green;
  color: #ffffff;
  font-weight: bold;
}
