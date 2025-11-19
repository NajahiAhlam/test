Great \u2014 I understand the new requirement clearly.
Let me rewrite it cleanly and then give you the **backend model + method + DTO updates + front logic** so you can store the choice and update classement properly.

---

# \u2705 **Your New Requirement**

After generating `EstimationResult`, the user must choose **one fournisseur**:

### \u2714 If the chosen fournisseur already has `classement = 1`

\u27a1 nothing changes.

### \u2714 If the chosen fournisseur has `classement \u2260 1`

\u27a1 Then reorder classement like this:

* The chosen fournisseur becomes **classement = 1**
* The old fournisseur who was `classement = 1` becomes **classement = 2**
* All others shift down by 1 or stay as they were (based on your logic \u2014 usually unchanged except old #1)

\u27a1 Store this final selection in the database:

* Chosen fournisseur
* Comment (if not rank 1)
* New classement update

---

# \u2705 **1. Add database entity to store user choice**

You need a table like:

### **`FournisseurSelection`** (one per project)

```java
@Entity
public class FournisseurSelection {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne
    private Projet projet;

    private String fournisseur;  // chosen supplier
    private Integer classementFinal; // always 1

    private String commentaire; // only if not original rank 1

    private LocalDateTime dateSelection;
}
```

---

# \u2705 **2. Add backend endpoint to save choix + update classement**

### **Controller**

```java
@PostMapping("/{projetId}/choix-fournisseur")
public ResponseEntity<Void> chooseFournisseur(
        @PathVariable Long projetId,
        @RequestBody FournisseurChoiceDTO dto) {

    adjudicationService.chooseFournisseur(projetId, dto);
    return ResponseEntity.ok().build();
}
```

### **DTO**

```java
@Data
public class FournisseurChoiceDTO {
    private String fournisseur;
    private String comment;
}
```

---

# \u2705 **3. Service logic: update classement + save selection**

```java
@Override
public void chooseFournisseur(Long projetId, FournisseurChoiceDTO dto) {

    Projet projet = projetRepository.findById(projetId)
            .orElseThrow(() -> new RuntimeException("Projet introuvable"));

    List<ArticleProjetPrice> list = articleProjetPriceRepository
            .findAllByProjetIdOrderByClassement(projetId);

    // Find old rank 1
    ArticleProjetPrice oldRank1 = list.stream()
        .filter(r -> r.getClassement() == 1)
        .findFirst()
        .orElse(null);

    // Find chosen fournisseur
    ArticleProjetPrice chosen = list.stream()
        .filter(r -> r.getFournisseur().equals(dto.getFournisseur()))
        .findFirst()
        .orElseThrow(() -> new RuntimeException("Fournisseur not found"));

    // If chosen already rank 1 \u2192 nothing to do
    if (chosen.getClassement() != 1) {

        // Update classement
        chosen.setClassement(1);

        if (oldRank1 != null) {
            oldRank1.setClassement(2);
        }

        // Save changes
        articleProjetPriceRepository.save(chosen);
        if (oldRank1 != null) articleProjetPriceRepository.save(oldRank1);
    }

    // Save selection decision
    FournisseurSelection fs = new FournisseurSelection();
    fs.setProjet(projet);
    fs.setFournisseur(dto.getFournisseur());
    fs.setClassementFinal(1);
    fs.setCommentaire(dto.getComment());
    fs.setDateSelection(LocalDateTime.now());

    fournisseurSelectionRepository.save(fs);
}
```

---

# \u2705 **4. FRONT: send the chosen fournisseur to backend**

In your component:

```ts
saveFournisseurChoice(fournisseur: string, comment: string | null) {

  const body = {
    fournisseur: fournisseur,
    comment: comment
  };

  this.adjudicationService.chooseFournisseur(this.projetId, body)
    .subscribe({
      next: () => {
        this._utilService.displaySuccess("Choix enregistré avec succès", "Succès");
      },
      error: () => {
        this._utilService.displayError("Erreur lors de l'enregistrement", "Erreur");
      }
    });
}
```

### In your `GestionAdjudicationService`

```ts
chooseFournisseur(projetId: number, body: any): Observable<void> {
  return this.http.post<void>(`${BASE_URL}/adjudication/${projetId}/choix-fournisseur`, body);
}
```

---

# \u2705 **5. Front logic for selecting fournisseur**

Your `onChooseFournisseur()` is already good:

```ts
onChooseFournisseur() {
  if (!this.selectedFournisseur) return;

  const classement = this.classementRow[
    this.fournisseurs.indexOf(this.selectedFournisseur)
  ];

  if (classement === 1) {
    this.saveFournisseurChoice(this.selectedFournisseur, null);
  } else {
    this.windowService.open(JustificationDialogComponent, {
      context: { fournisseur: this.selectedFournisseur }
    })
    .onClose.subscribe(comment => {
      if (comment) {
        this.saveFournisseurChoice(this.selectedFournisseur!, comment);
      }
    });
  }
}
```

---

# \U0001f389 **DONE \u2014 now your app can:**

\u2714 Let user select fournisseur
\u2714 Ask justification when choice is not classement 1
\u2714 Update classement in database (swap rank)
\u2714 Save user decision in `FournisseurSelection` table
\u2714 Keep it linked to the project

---

If you want, I can also generate:

\u2705 The HTML for the justification popup
\u2705 The entity repository
\u2705 The entire backend service with full error handling

Just say **"generate everything"**.
