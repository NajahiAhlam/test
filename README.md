<thead> 
                    <tr>
                        <th style=" background:#fff; border: #fff;"></th> 
                        <!-- Display columns based on selected program and project -->
                        <ng-container *ngIf="selectedProgram === 'Buffer' || (selectedProgram === 'Centre de Compétence' && selectedProject === 'Ets factory')">
                            <th style="text-align: center;" colspan="2">SOL RETAIL</th>
                            <th style="text-align: center;" colspan="2">SOL PHOENIX</th>
                            <th style="text-align: center;" colspan="2">ITS</th>
                            <th style="text-align: center;" colspan="2">COO TECH</th>
                        </ng-container>
                        <!-- Display columns based on selected program and project -->
                        <ng-container *ngIf="selectedProgram === 'Centre de Compétence' && selectedProject !== 'Ets factory'">
                            <th style="text-align: center;" colspan="2">ITS</th>
                        </ng-container>
                        <!-- Display columns based on selected program -->
                        <ng-container *ngIf="selectedProgram === 'Centre de Compétence' || selectedProject === 'Refonte EST'">
                            <th style="text-align: center;" colspan="2">GOV IT</th>
                        </ng-container>
                    </tr>
                    <tr>
                        <th style=" background:#fff; border: #fff;"></th>
                        <th>interne</th>
                        <th>externe</th>
                        <th>interne</th>
                        <th>externe</th>
                        <th>interne</th>
                        <th>externe</th>
                        <th>interne</th>
                        <th>externe</th>
                    </tr>
                </thead>
