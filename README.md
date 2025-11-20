Perfect \u2014 I understand exactly what you want now.
You want a **re-ranking** logic when the user chooses a fournisseur in the UI:

### \u2705 Requirements

1. If the chosen fournisseur already has **classement = 1**, then nothing changes.
2. If the chosen fournisseur is **not rank 1**, then:

   * Set the chosen fournisseur \u2192 **classement = 1**
   * The previous rank 1 fournisseur becomes rank 2
   * All others get rank = their current rank + 1 (sorted by total or keep same order)
3. Send the **updated list of EstimationFournisseurDTO** to backend in a new API call:

   ```ts
   EstimationResult { result: EstimationFournisseurDTO[] }
   ```

---

# \u2705 FRONTEND (Angular) \u2014 Do the classement update

Add a method `reorderClassement(selectedFournisseur: string)` in your component:

```ts
private reorderClassement(fournisseur: string) {
  const result = this.data$!.result;

  // Find the selected fournisseur DTO
  const selected = result.find(r => r.fournisseur === fournisseur);
  if (!selected) return result;

  // If already rank 1, no change
  if (selected.classement === 1) {
    return result;
  }

  // Get the one that is currently rank 1
  const oldRank1 = result.find(r => r.classement === 1);

  // Assign new ranks
  selected.classement = 1;

  if (oldRank1) {
    oldRank1.classement = 2;
  }

  // Update all others: shift ranks based on total
  const others = result
    .filter(r => r !== selected && r !== oldRank1)
    .sort((a, b) => a.total - b.total);

  let rank = 3;
  for (let r of others) {
    r.classement = rank++;
  }

  // Return updated list sorted by classement
  return [...result].sort((a, b) => a.classement - b.classement);
}
```

---

# \u2705 Call this method inside `onChooseFournisseur()`

```ts
onChooseFournisseur() {
  if (!this.selectedFournisseur) return;

  const classement = this.classementRow[
    this.fournisseurs.indexOf(this.selectedFournisseur)
  ];

  if (classement === 1) {
    // user selected the already #1 fournisseur \u2192 directly save
    this.sendUpdatedRanking(this.data$!.result);
    return;
  }

  this.windowService.open(JustificationDialogComponent, {
    context: { fournisseur: this.selectedFournisseur }
  })
  .onClose.subscribe(comment => {
    if (comment) {
      const newRanking = this.reorderClassement(this.selectedFournisseur!);
      this.sendUpdatedRanking(newRanking, comment);
    }
  });
}
```

---

# \u2705 Implement `sendUpdatedRanking()`

```ts
private sendUpdatedRanking(newList: EstimationFournisseurDTO[], comment?: string) {
  const payload: EstimationResult = { result: newList };

  this.adjudicationService.updateClassement(this.projetId, payload, comment)
    .subscribe({
      next: () => {
        this._utilService.displaySuccess("Classement mis à jour", "Succès");
        this.calculateEstimation(); // refresh UI
      },
      error: () => {
        this._utilService.displayError("Erreur lors de la mise à jour", "Erreur");
      }
    });
}
```

---

# \u2705 BACKEND \u2014 Create the updateClassement endpoint

### Controller:

```java
@PostMapping("/estimation/{projetId}/update")
public ResponseEntity<Void> updateClassement(
        @PathVariable Long projetId,
        @RequestBody EstimationResult estimationResult,
        @RequestParam(required = false) String comment
) {
    estimationService.updateClassement(projetId, estimationResult.getResult(), comment);
    return ResponseEntity.ok().build();
}
```

---

# \u2705 Service Logic

```java
@Transactional
public void updateClassement(Long projetId, List<EstimationFournisseurDTO> newList, String comment) {

    List<EstimationFournisseurResult> saved =
            estimationResultRepository.findByProjetId(projetId);

    for (EstimationFournisseurDTO dto : newList) {
        EstimationFournisseurResult e = saved.stream()
                .filter(x -> x.getFournisseur().getRaisonSociale().equals(dto.getFournisseur()))
                .findFirst()
                .orElseThrow(() -> new RuntimeException("Fournisseur not found in estimation"));

        e.setClassement(dto.getClassement());
        // Optional: store justification comment if classement != 1
        // e.setJustification(comment);
    }

    estimationResultRepository.saveAll(saved);
}
```

---

# \u2705 What you get now

\u2714 When user chooses a fournisseur
\u2714 Classements update properly
\u2714 The UI sends a clean `EstimationResult` DTO structured like this:

```json
{
  "result": [
    { "fournisseur": "F1", "estimationCC": 10000, "estimationHB": 5000, "total": 15000, "classement": 1 },
    { "fournisseur": "F2", "estimationCC": 12000, "estimationHB": 5000, "total": 17000, "classement": 2 },
    ...
  ]
}
```

\u2714 Backend updates the DB accordingly
\u2714 No recomputation \u2014 only classement changes

---

If you want, I can also send you a **simplified version**, or create a **before/after ranking preview** in the UI.
