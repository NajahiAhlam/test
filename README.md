<td *ngFor="let col of columns">
  <ng-container [ngSwitch]="col.key">

    <!-- Handle 'validateur' as special case -->
    <ng-container *ngSwitchCase="'validateur'">
      {{ row.validateur?.prenom }} {{ row.validateur?.nom }}
    </ng-container>

    <!-- Default case -->
    <ng-container *ngSwitchDefault>
      {{ row[col.key] }}
    </ng-container>

  </ng-container>
</td>
