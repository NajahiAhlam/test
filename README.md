<nb-card-body>
  <div id="pdfContent">
    <!-- ====================== PAGE 1 ====================== -->
    <div class="pdf-header">
      <div class="page-guard">
        <div class="page">
          <div class="bank">SAHAM BANK</div>

          <div class="center-block">
            <div class="line title">AMENAGEMENT DE L'AGENCE BANCAIRE</div>
            <div class="line subtitle">{{ resultF?.projet?.nom }}</div>
            <div class="line contract">CONTRAT CADRE ( LOT UNIQUE )</div>
          </div>

          <div class="footer">QUANTITATIF</div>
        </div>
      </div>
    </div>

    <!-- ====================== PAGE 2 ====================== -->
    <div class="page-guard">
      <div class="page">
        <!-- Form Start -->
        <form [formGroup]="form" (ngSubmit)="onSubmit()">
          <!-- ===== Nature de la dépense ===== -->
          <div class="naturedep mt-3 ml-5">
            <h6 class="text-center mt-3">Nature de la dépense</h6>

            <div class="form-group">
              <label>Objet de la dépense</label>
              <textarea class="natdep" formControlName="objetDepense" placeholder="à remplir ..." required></textarea>
            </div>

            <div class="form-group">
              <label>Données géométriques et contenance</label>
              <textarea class="natdep" formControlName="donneesGeometriques" placeholder="à remplir ..." required></textarea>
            </div>

            <div class="form-group">
              <label>Consistance des travaux</label>
              <textarea class="natdep" formControlName="consistanceTravaux" required></textarea>
            </div>

            <div class="form-group">
              <label>Programme détaillé transmis par la ligne métier</label>
              <textarea class="natdep" formControlName="programmeTransmis" placeholder="à remplir ..." required></textarea>
            </div>
          </div>

          <!-- Team Section -->
          <div class="team-section">
            <label class="section-title">Équipe Immo :</label>

            <div class="row">
              <div class="col-4">
                <label class="text-secondary d-block mb-1">Chef de Projet :</label>
                <input class="natdep" formControlName="chefDeProjet" placeholder="à remplir ..." required />
              </div>

              <div class="col-4">
                <label class="text-secondary d-block mb-1">Acheteur Délégué :</label>
                <input class="natdep" formControlName="acheteurDelegue" placeholder="à remplir ..." required />
              </div>

              <div class="col-4">
                <label class="text-secondary d-block mb-1">Responsable Département :</label>
                <input class="natdep" formControlName="responsableDepartement" placeholder="à remplir ..." required />
              </div>
            </div>
          </div>

          <!-- ===== Intervenants ===== -->
          <div class="intervenants-section mb-4">
            <label>Intervenants et équipe du maître d'oeuvre</label>

            <div class="row">
              <div class="col-4">
                <label class="text-secondary">Architecte :</label>
                <input class="natdep" formControlName="architecte" placeholder="Architecte..." required />
              </div>

              <div class="col-4">
                <label class="text-secondary">Architecte d'intérieur :</label>
                <input class="natdep" formControlName="architecteInterieur" placeholder="Architecte d'intérieur..." required />
              </div>

              <div class="col-4">
                <label class="text-secondary">Métreur :</label>
                <input class="natdep" formControlName="metreur" placeholder="Métreur..." required />
              </div>
            </div>

            <div class="row">
              <div class="col-6">
                <label class="text-secondary">BET :</label>
                <input class="natdep" formControlName="bet" placeholder="BET..." required />
              </div>

              <div class="col-6">
                <label class="text-secondary">BC :</label>
                <input class="natdep" formControlName="bc" placeholder="BC..." required />
              </div>
            </div>
          </div>

          <!-- Fiche N° -->
          <div class="form-group">
            <label>N° Fiche :</label>
            <input class="text-center border-bottom border-light" formControlName="ficheNumber" placeholder="N° Fiche..." />
          </div>

          <!-- Submit Button -->
          <button type="submit" [disabled]="form.invalid" class="btn btn-primary">Save</button>
        </form>
      </div>
    </div>
  </div>
</nb-card-body>
