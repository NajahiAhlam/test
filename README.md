Ah! Now I understand your full scenario. You basically have **two phases**:

1. **Calculation phase**: Compute estimations (`EstimationFournisseurDTO`) dynamically for all suppliers, calculate totals, rank them, and **show them to the user** in a step.
2. **Selection phase**: User picks **which fournisseur to keep**, typically the one with `classement = 1`, but they could choose another. Then, this choice needs to be **saved in the database**.

Currently, your service just calculates the DTOs and returns them \u2014 nothing is persisted. That\u2019s fine for **display**, but you need a way to **persist the selected fournisseur** after the user confirms.

Let me outline a clean approach.

---

## **1\ufe0f\u20e3 Keep calculation separate from persistence**

Your `calculateEstimation()` should stay **pure** \u2014 it calculates, sorts, ranks, and returns DTOs. No database saving. This is good for **stepper display**.

---

## **2\ufe0f\u20e3 Add a \u201cchosen fournisseur\u201d table/entity**

You need an entity to store the **selected fournisseur per projet**, e.g.:

```java
@Entity
@Table(name = "projet_fournisseur_selection")
@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class ProjetFournisseurSelection {
    
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne
    @JoinColumn(name = "projet_id")
    private Projet projet;

    @ManyToOne
    @JoinColumn(name = "fournisseur_id")
    private Fournisseur fournisseur;

    private boolean selected; // true if user chooses this one
}
```

---

## **3\ufe0f\u20e3 After user selects**

Create a service method to **persist their choice**:

```java
public void saveSelectedFournisseur(Long projetId, Long fournisseurId) {
    Projet projet = projetRepository.findById(projetId)
            .orElseThrow(() -> new RuntimeException("Projet not found"));
    Fournisseur fournisseur = fournisseurRepository.findById(fournisseurId)
            .orElseThrow(() -> new RuntimeException("Fournisseur not found"));

    // Optional: remove old selection
    projetFournisseurSelectionRepository.deleteByProjetId(projetId);

    ProjetFournisseurSelection selection = ProjetFournisseurSelection.builder()
            .projet(projet)
            .fournisseur(fournisseur)
            .selected(true)
            .build();

    projetFournisseurSelectionRepository.save(selection);
}
```

---

## **4\ufe0f\u20e3 UI Flow in Stepper**

### Step 1 \u2013 Show Estimation Table

* Call `calculateEstimation(projetId)` \u2192 returns `EstimationResultDTO`.
* Display in table:

| Fournisseur | Estimation CC | Estimation HB | Total | Classement | Select? |
| ----------- | ------------- | ------------- | ----- | ---------- | ------- |

* Show a **radio button** or **select** next to each row for the user to choose.

---

### Step 2 \u2013 Save selection

* User clicks \u201cConfirm\u201d \u2192 send selected `fournisseurId` to backend.
* Call `saveSelectedFournisseur(projetId, fournisseurId)` \u2192 persist in database.

---

## **5\ufe0f\u20e3 Optional: Pre-select the Classement 1 fournisseur**

In the frontend:

```ts
const bestFournisseur = estimationResult.result.find(r => r.classement === 1);
this.selectedFournisseurId = bestFournisseur?.id;
```

* User can change it if needed.
* When saving, always send the selected ID.

---

## **6\ufe0f\u20e3 Advantages**

* Calculation stays **pure** \u2192 easy to test.
* User sees dynamic results **without saving intermediate calculations**.
* Database only stores the **final selected supplier** \u2192 keeps history clean.
* You can add multiple selections in the future if needed.

---

If you want, I can **write the full example of the Stepper + Angular table**:

* Shows `EstimationResultDTO`
* Auto-selects `classement = 1` fournisseur
* User can change selection
* Backend receives selection and persists it

It will be ready-to-use in your existing project.

Do you want me to do that?
