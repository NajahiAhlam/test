<table class="table w-100">
  <thead class="bg-header fw-bold">
    <tr>
      <th *ngFor="let col of columns">{{ col.label | titlecase }}</th>
    </tr>
    <tr>
      <th *ngFor="let col of columns">
        <input
          nbInput
          fullWidth
          class="form-control"
          [(ngModel)]="columnFilters[col.key]"
          (ngModelChange)="onColumnFilterChange()"
          placeholder="{{ col.label }}"
        />
      </th>
    </tr>
  </thead>
  <tbody>
    <ng-container *ngFor="let group of groupedRows">
      <tr *ngFor="let row of group.rows; let i = index" [class.grouped-row]="true">
        
        <td *ngFor="let col of columns; let c = index">

          <!-- assigneeName with rowspan -->
          <ng-container *ngIf="col.key === 'assigneeName'">
            <span *ngIf="i === 0" [attr.rowspan]="group.rowspan" class="assignee-cell">
              {{ group.assigneeName | mail }}
            </span>
          </ng-container>

          <!-- NbrS summed with rowspan -->
          <ng-container *ngIf="col.key === 'NbrS'">
            <span *ngIf="i === 0" [attr.rowspan]="group.rowspan" class="nbrs-cell">
              {{ group.sumNbrS }}
            </span>
          </ng-container>

          <!-- other columns -->
          <ng-container *ngIf="col.key !== 'assigneeName' && col.key !== 'NbrS'">
            <ng-container [ngSwitch]="col.key">

              <ng-container *ngSwitchCase="'validateur'">
                {{ row[col.key] | mail }}
              </ng-container>

              <ng-container *ngSwitchCase="'categorie'">
                {{ getCategorieClass(row[col.key]) }}
              </ng-container>

              <ng-container *ngSwitchCase="'productStatus'">
                {{ getActionLabel(row[col.key]) }}
              </ng-container>

              <ng-container *ngSwitchDefault>
                {{ row[col.key] }}
              </ng-container>

            </ng-container>
          </ng-container>

        </td>
      </tr>

      <!-- Optional separator row between groups -->
      <tr class="group-separator"><td [attr.colspan]="columns.length"></td></tr>
    </ng-container>
  </tbody>
</table>
/* Highlight entire grouped rows background */
tbody tr.grouped-row {
  background-color: #fafafa;
}

tbody tr.grouped-row td {
  border-bottom: none;
}

tbody tr.grouped-row:first-child td {
  border-bottom: 1px solid #ccc; /* or your border style */
}


/* Border and vertical align for assigneeName cell */
.assignee-cell {
  border-right: 2px solid #ddd;
  vertical-align: middle;
  font-weight: 600;
}

/* Style for NbrS summed cell */
.nbrs-cell {
  vertical-align: middle;
  font-weight: 600;
  text-align: center;
}

/* Separator row between groups */
.group-separator td {
  border-bottom: 2px solid #999;
  padding: 0;
  margin: 0;
  height: 8px;
  background-color: transparent;
}
