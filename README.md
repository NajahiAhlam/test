Sure! I will **clean**, **re-structure**, and **organize** your HTML **without touching the first `<div id="pdfContent">`**.
I will improve:

\u2714 Page separation
\u2714 Indentation
\u2714 Table structure
\u2714 Remove repeating `id="header-ded"` (ids must be unique)
\u2714 Organize sections in semantic blocks
\u2714 Prepare the structure for clean PDF generation
\u2714 Keep ALL your fields, labels, classes

---

# \u2705 **Here is your CLEAN + WELL-STRUCTURED HTML**

\U0001f449 **NOTE:**

* I **did not change** `<div id="pdfContent">` at all.
* I only restructured **inside** it.

---

```html
<nb-card>
  <nb-card-header class="d-flex flex-row justify-content-end">
    <button nbButton (click)="generatePDF()">Download PDF</button>
  </nb-card-header>

  <nb-card-body>
    <div id="pdfContent">

      <!-- ====================== PAGE 1 ====================== -->
      <div class="pdf-header">
        <div class="page-guard">
          <div class="page">
            <div class="bank">SAHAM BANK</div>

            <div class="center-block">
              <div class="line title">AMENAGEMENT DE L'AGENCE BANCAIRE</div>
              <div class="line subtitle">GUEZNAYA</div>
              <div class="line contract">CONTRAT CADRE ( LOT UNIQUE )</div>
            </div>

            <div class="footer">QUANTITATIF</div>
          </div>
        </div>
      </div>

      <!-- ====================== PAGE 2 ====================== -->
      <div class="page-guard">
        <div class="page">

          <!-- ===== Header Table ===== -->
          <div class="header-section">
            <table class="table header-table">
              <thead>
                <tr>
                  <th rowspan="2" class="w-25">Direction des Moyens Généraux</th>

                  <th rowspan="2" class="w-50 text-center">
                    FICHE DED :
                    <span class="bold text-uppercase"></span><br /><br />
                    <input type="text" readonly
                      class="text-center border-bottom border border-light" />
                  </th>

                  <th class="w-25"></th>

                  <th rowspan="2">
                    <img class="logo" src="\assets\img\image (1).png" alt="Saham Bank" />
                  </th>
                </tr>

                <tr>
                  <th class="w-25">
                    N° Fiche:
                    <input class="text-center" type="text"
                      style="border: none; background-color: transparent"
                      placeholder="N° Fiche..." />
                  </th>
                </tr>
              </thead>
            </table>
          </div>

          <!-- ===== Nature de la dépense ===== -->
          <div class="naturedep mt-3 ml-5">
            <h6 class="text-center mt-3">Nature de la dépense</h6>

            <div class="form-group">
              <label>Objet de la dépense</label>
              <textarea class="natdep" placeholder="à remplir ..." required></textarea>
            </div>

            <div class="form-group">
              <label>Données géométriques et contenance</label>
              <textarea class="natdep" placeholder="à remplir ..." required></textarea>
            </div>

            <div class="form-group">
              <label>Consistance des travaux</label>
              <textarea class="natdep" required></textarea>
            </div>

            <div class="form-group">
              <label>Programme détaillé transmis par la ligne métier</label>
              <textarea class="natdep" placeholder="à remplir ..." required></textarea>
            </div>

            <!-- Team Section -->
            <div class="team-section">
              <label>Équipe Immo :</label>
              <div class="row">
                <div class="col-4">
                  <label class="text-secondary">Chef de Projet :</label>
                  <input class="natdep" disabled placeholder="à remplir ..." required />
                </div>

                <div class="col-4">
                  <label class="text-secondary">Acheteur Délégué :</label>
                  <input class="natdep" disabled placeholder="à remplir ..." required />
                </div>

                <div class="col-4">
                  <label class="text-secondary">Responsable Departement :</label>
                  <input class="natdep" disabled placeholder="à remplir ..." required />
                </div>
              </div>
            </div>

          </div>
        </div>
      </div>

      <!-- ====================== PAGE 3 ====================== -->
      <div class="page-guard">
        <div class="page">

          <!-- ===== Intervenants ===== -->
          <div class="intervenants-section mb-4">
            <label>Intervenants et équipe du maître d'\u0153uvre</label>

            <div class="row">
              <div class="col-4">
                <label class="text-secondary">Architecte :</label>
                <input class="natdep" placeholder="Architecte..." required />
              </div>

              <div class="col-4">
                <label class="text-secondary">Architecte d'intérieur :</label>
                <input class="natdep" placeholder="Architecte d'intérieur..." required />
              </div>

              <div class="col-4">
                <label class="text-secondary">Métreur :</label>
                <input class="natdep" placeholder="Métreur..." required />
              </div>
            </div>

            <div class="row">
              <div class="col-6">
                <label class="text-secondary">BET :</label>
                <input class="natdep" placeholder="BET..." required />
              </div>

              <div class="col-6">
                <label class="text-secondary">BC :</label>
                <input class="natdep" placeholder="BC..." required />
              </div>
            </div>
          </div>

          <!-- ===== Prestataire ===== -->
          <div class="prestataire-section">
            <label>Informations sur le prestataire</label>

            <div class="row">
              <div class="col-4">
                <label class="text-secondary">Raison Sociale :</label>
                <input class="natdep" placeholder="Raison sociale..." required />
              </div>

              <div class="col-4">
                <label class="text-secondary">Montant Adjugé (MAD) :</label>
                <input class="natdep" placeholder="Montant..." required />
              </div>

              <div class="col-4">
                <label class="text-secondary">Chiffre d'Affaires :</label>
                <input class="natdep" placeholder="CA..." required />
              </div>
            </div>

            <div class="row">
              <div class="col-6">
                <label class="text-secondary">Expertise :</label>
                <input class="natdep" placeholder="Expertise..." required />
              </div>

              <div class="col-6">
                <label class="text-secondary">Anciennes Transactions :</label>
                <input class="natdep" placeholder="Transactions..." required />
              </div>
            </div>

            <div class="row">
              <div class="col-6">
                <label class="text-secondary">Réputation :</label>
                <input class="natdep" placeholder="Réputation..." required />
              </div>

              <div class="col-6">
                <label class="text-secondary">Frais de Déplacement :</label>
                <input class="natdep" placeholder="Frais..." required />
              </div>
            </div>

          </div>

        </div>
      </div>

    </div>
  </nb-card-body>
</nb-card>
```

---

# \u2705 If you want, I can also:

\u2714 Add **CSS** for perfect PDF pages
\u2714 Add **page breaks** that work with html2pdf/JSPDF
\u2714 Add a **better layout**
\u2714 Make it look **like a real bank PDF**
\u2714 Fix your PDF generation errors

Just tell me!
