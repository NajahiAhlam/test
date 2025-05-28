Can't bind to 'nbSort' since it isn't a known property of 'table'.ngtsc(-998002)
pilotage-conditions.component.ts(11, 18): Error occurs in the template of component PilotageConditionsComponent.
<nb-card>
  <nb-card-header>
    Grouped Table (Flat View with Filter)
  </nb-card-header>

  <nb-card-body>

    <input
      nbInput
      fullWidth
      placeholder="Filter..."
      [(ngModel)]="filterText"
      (ngModelChange)="onFilterChange()"
    />

    <table [nbSort]="true">
      <thead>
        <tr>
          <th *ngFor="let col of columns">{{ col | titlecase }}</th>
        </tr>
      </thead>
      <tbody>
        <tr *ngFor="let row of filteredData">
          <td *ngFor="let col of columns">
            {{ row[col] }}
          </td>
        </tr>
      </tbody>
    </table>
    
  </nb-card-body>
</nb-card>
