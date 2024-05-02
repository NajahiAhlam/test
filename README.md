<!-- Your HTML template -->
<nb-select style="float: right;" class="m-0 " id="type" placeholder="Select lead" (selectedChange)="changeLocation($event)">
    <nb-option value="Prestation Régie">Prestation Régie</nb-option>
    <nb-option value="Prestation SGABS">Prestation SGABS</nb-option>
    <nb-option value="Matériel-logiciel">Matériel-logiciel</nb-option>
    <nb-option value="Prestation Forfait">Prestation Forfait</nb-option>
</nb-select>

</div>

</nb-card-header>

<nb-card-body>

    <table aria-describedby="app-ref" class="w-100 table-hover bg-white" id="prestation-table">
        <thead>
            <tr>
                <th style="text-align: center;" colspan="7" id="changeth">{{ selectedPrestation }}</th>
            </tr>
            <tr>
                <th>Alloué</th>
                <th>Engagé</th>
                <th>Target</th>
                <th>Consommé</th>
                <th>Target</th>
                <th>Facturé</th>
                <th>Target</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td>{{ allocated }}</td>
                <td>{{ engaged }}</td>
                <td>{{ target1 }}</td>
                <td>{{ consumed }}</td>
                <td>{{ target2 }}</td>
                <td>{{ billed }}</td>
                <td>{{ target3 }}</td>
            </tr>
        </tbody>
    </table>

</nb-card-body>
---------------------------------------------------------------------
// Your component code
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-budget-analysis',
  templateUrl: './budget-analysis.component.html',
  styleUrls: ['./budget-analysis.component.scss']
})
export class BudgetAnalysisComponent implements OnInit {
  selectedPrestation!: string;
  allocated: number = 0;
  engaged: number = 0;
  target1: number = 0;
  consumed: number = 0;
  target2: number = 0;
  billed: number = 0;
  target3: number = 0;

  changeLocation(selected: string): void {
    this.selectedPrestation = selected;
    // Here you should update the values of allocated, engaged, target1, consumed, target2, billed, and target3
    // based on the selected prestation
    // For example:
    if (selected === 'Prestation Régie') {
      this.allocated = 100;
      this.engaged = 80;
      // Update other values similarly
    } else if (selected === 'Prestation SGABS') {
      // Assign values for this option
    }
    // Repeat the same for other options
  }

  constructor() { }

  ngOnInit(): void {
    // Initialize default values or fetch initial data here if needed
  }
}
