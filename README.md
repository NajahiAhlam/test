To dynamically handle date inputs in your table, you can modify the `columns` array to include a `type` property for each column that represents the type of input for that column, such as `text`, `number`, or `date`. Based on the `type`, you can conditionally render the appropriate input element inside the table.

Here's how you can adjust your code to support dynamic date filtering in the table:

1. **Columns Configuration**: Update the columns configuration to explicitly specify `type: 'date'` for the date-related columns like `createdAt` and `updatedAt`.

   ```typescript
   columns = [
     {key: 'nom', label: 'Nom', type: 'text'},
     {key: 'budgetEstime', label: 'Budget estimé', type: 'number'},
     {key: 'surface', label: 'Surface', type: 'number'},
     {key: 'localisationProjet', label: 'Ville', type: 'text'},
     {key: 'consistence', label: 'Consistence', type: 'text'},
     {key: 'familleProjetNom', label: 'Famille de Projet', type: 'text'},
     {key: 'createdAt', label: 'Date de création', type: 'date'},
     {key: 'updatedAt', label: 'Mis à jour', type: 'date'},
   ];
   ```

2. **Dynamic Template Rendering**: In the HTML template, check if the column `type` is `date`, and if so, render the `nb-datepicker`. The rest of the columns can use a generic input field or select dropdown.

   Here\u2019s how you can modify the dynamic table template:

   ```html
   <ng-template [ngIf]="data">
     <table class="table w-100">
       <thead class="bg-header fw-bold">
         <tr>
           <td *ngFor="let column of columns" 
               (click)="column.sortable !== false ? sortBy(column.key) : null"
               [class.sort-disabled]="column.sortable === false"
               class="p-3 border border-end-white">
             <div class="d-flex justify-content-between">
               <div>{{ column.label }}</div>
               <div [ngClass]="(keyword == column.key) ? 'text-black' : 'd-none'">
                 <span *ngIf="direction">
                   <nb-icon icon="arrow-up-line"></nb-icon>
                 </span>
                 <span *ngIf="!direction">
                   <nb-icon icon="arrow-down-line"></nb-icon>
                 </span>
               </div>
             </div>
           </td>
           <td *ngIf="action" colspan="2" class="p-3 border border-end-white">Actions</td>
         </tr>
       </thead>
       <tbody>
         <tr>
           <td *ngFor="let column of columns">
             <form [formGroup]="searchFormGroup">
               <ng-container *ngIf="column.searchable !== false" [ngSwitch]="column.key">
                 <!-- Specific case for 'enabled' column -->
                 <ng-container *ngSwitchCase="'enabled'">
                   <nb-select formControlName="{{column.key}}" placeholder="{{column.label}}" (selectedChange)="onSearch()" fullWidth>
                     <nb-option value="">Tous</nb-option>
                     <nb-option value="true">Activé</nb-option>
                     <nb-option value="false">Désactivé</nb-option>
                   </nb-select>
                 </ng-container>
                 
                 <!-- Specific case for date type columns -->
                 <ng-container *ngSwitchCase="'createdAt'">
                   <input fullWidth class="form-control" [nbDatepicker]="datepicker_createdAt" name="createdAt" id="createdAt" formControlName="createdAt" placeholder="Date de création" (click)="onSearch()" />
                   <nb-datepicker #datepicker_createdAt format="dd/MM/yyyy"></nb-datepicker>
                 </ng-container>
                 
                 <ng-container *ngSwitchCase="'updatedAt'">
                   <input fullWidth class="form-control" [nbDatepicker]="datepicker_updatedAt" name="updatedAt" id="updatedAt" formControlName="updatedAt" placeholder="Date de Modification" (click)="onSearch()" />
                   <nb-datepicker #datepicker_updatedAt format="dd/MM/yyyy"></nb-datepicker>
                 </ng-container>
                 
                 <!-- General case for other columns -->
                 <ng-container *ngSwitchDefault>
                   <input *ngIf="column.type === 'text'" formControlName="{{column.key}}" class="form-control" placeholder="{{column.label}}" (input)="onSearch()" />
                   <input *ngIf="column.type === 'number'" formControlName="{{column.key}}" class="form-control" placeholder="{{column.label}}" type="number" (input)="onSearch()" />
                 </ng-container>
               </ng-container>
             </form>
           </td>
           <td *ngIf="action">
             <a class="text-decoration-none float-right">
               <button type="button" class="d-flex btn btn-dark m-auto" (click)="onAddNewObject()">
                 <div class="addNewUser">Ajouter</div>
               </button>
             </a>
           </td>
         </tr>
         <!-- Render table rows -->
         <tr *ngFor="let item of data.content">
           <td *ngFor="let column of columns" class="px-4" (click)="rowClicked.emit(item)">
             {{ getColumnValue(item, column.key) }}
           </td>
           <td class="editIcon text-center" *ngIf="action">
             <nb-icon class="btn-details edit-icon" style="color:darkorange" icon="edit-line" (click)="editItem.emit(item)"></nb-icon>
             <nb-icon class="btn-details delete-icon" icon="spam-3-line" style="color: red" (click)="deleteItem.emit(item)"></nb-icon>
           </td>
         </tr>
         <ng-template [ngIf]="!data?.content?.length">
           <tr>
             <td class="px-4" [attr.colspan]="columns.length" style="text-align: center;">
               <div style="display: flex; flex-direction: column; align-items: center;">
                 <nb-icon icon="folder-reduce-line" style="font-size: 30px; color: rgb(65, 62, 62);margin-top: 10px;" class="mb-2"></nb-icon>
                 <p>Aucun élément trouvée pour le moment</p>
               </div>
             </td>
           </tr>
         </ng-template>
       </tbody>
     </table>
   </ng-template>
   ```

3. **Form Control Setup**: Ensure that the `searchFormGroup` is properly initialized with form controls corresponding to each of the columns. You already have this part in your component:

   ```typescript
   ngOnInit(): void {
     this.searchFormGroup = this.fb.group({});
     this.columns.forEach(column => {
       if (column.searchable !== false) {
         this.searchFormGroup.addControl(column.key, this.fb.control(''));
       }
     });

     this.searchFormGroup.valueChanges.pipe(
       debounceTime(1000)
     ).subscribe(() => {
       if (this.formValue !== this.searchFormGroup.value) {
         this.formValue = this.searchFormGroup.value;
         this.searchForm.emit(this.searchFormGroup.value);
       }
     });
   }
   ```

4. **Date Formatting and Search**: Ensure that the `onSearch()` method is correctly implemented to filter based on the date range or specific date values. Depending on your backend logic, you can send the selected dates and other form values for filtering.

With this setup, you will have date pickers in the columns where the `type` is set to `date`, and the search functionality will work dynamically across the entire table.
