Got it \U0001f44d \u2014 your two flows (`moveToNextTask` and `claimRequest`) share a big part of the same skeleton:

* find `DemandeQualification`
* compute the new status
* build a decision param map
* call `submit`
* update the status
* save & write history

Only the **status logic**, **decision string**, and maybe the **history action** differ.

Here\u2019s how you can refactor this to be more **dynamic** and **DRY**:

---

### 1\ufe0f\u20e3 Create a generic transition method

```java
@Transactional
public DemandeQualificationResponse handleTransition(
        Long demandeId,
        WfStatus newStatus,
        String decision,
        UserAction action,
        Consumer<DemandeQualification> preTransitionLogic) throws PermissionException {

    // 1. Load demande
    DemandeQualification demande = demandeQualificationRepository.findById(demandeId)
            .orElseThrow(() -> new IllegalArgumentException("Aucune demande avec id = " + demandeId));

    // 2. Execute any extra logic before transition (assigning user, etc.)
    if (preTransitionLogic != null) {
        preTransitionLogic.accept(demande);
    }

    // 3. Build params & submit
    Map<String, String> submitParam = createSubmitParam(decision);
    ActivateProcessResult submit = submit(demande, submitParam);

    // 4. Update status
    updateDemandeStatus(demande, newStatus, submit);
    DemandeQualification updated = demandeQualificationRepository.save(demande);

    // 5. History
    demandeHistoryService.bpmnDemandeHistory(
            updated,
            submit,
            action,
            WfStatus.valueOf(demande.getStatus()), // old status
            newStatus
    );

    // 6. Return DTO (or entity)
    return new DemandeQualificationResponseMapper().mapToDTO(updated);
}
```

---

### 2\ufe0f\u20e3 Simplify `createSubmitParam`

```java
public Map<String, String> createSubmitParam(String decision) {
    Map<String, String> params = new HashMap<>();
    params.put("decision", decision);
    return params;
}
```

---

### 3\ufe0f\u20e3 Use it in your existing methods

#### `moveToNextTask`

```java
public DemandeQualification moveToNextTask(Long id) throws PermissionException {
    return handleTransition(
            id,
            WfStatus.A_PLANIFIER_KICKOFF,
            "A planifier",
            UserAction.SOUMETTRE_CORDINATOR,
            null // no pre-transition custom logic
    ).getDemande();
}
```

If you want to keep returning `DemandeQualification` instead of `DemandeQualificationResponse` you can have a second generic method returning the entity instead of the DTO.

---

#### `claimRequest`

```java
public DemandeQualificationResponse claimRequest(ClaimRequest request) throws PermissionException {
    User user = userService.getAuthenticatedUser();
    DemandeQualification demande = demandeQualificationRepository.findById(request.getRequestId())
            .orElseThrow(() -> new IllegalArgumentException("Aucune demande avec id = " + request.getRequestId()));

    WfStatus currentStatus = WfStatus.valueOf(demande.getStatus());
    WfStatus newStatus;
    String decision = "Prendre en charge";

    Consumer<DemandeQualification> preLogic = d -> {};

    switch (currentStatus) {
        case A_PLANIFIER_KICKOFF:
            newStatus = WfStatus.KICK_OFF;
            preLogic = d -> {
                if (d.getAssigne() == null || !Objects.equals(d.getAssigne().getEmail(), user.getEmail())) {
                    d.setAssigne(user);
                    d.setCoordinateurCNP(user);
                    createAssignDemande(false, user.getId(), d,
                            AssignType.COORDINATOR, user.getNom() + " " + user.getPrenom(), user.getEmail());
                }
            };
            break;

        case A_VALIDER_RESULTAT:
            newStatus = WfStatus.VALIDATION_RESULTAT;
            break;

        default:
            throw new IllegalStateException("Statut non supporté pour la prise en charge : " + demande.getStatus());
    }

    return handleTransition(
            request.getRequestId(),
            newStatus,
            decision,
            UserAction.TAKE_IN_CHARGE,
            preLogic
    );
}
```

---

### 4\ufe0f\u20e3 Benefits

* **Single place** for submit + status + history logic
* Flexible with `Consumer<DemandeQualification>` for custom per-transition logic
* Only pass what\u2019s different: `newStatus`, `decision`, `UserAction`, optional pre-logic
* Easier to test

---

Would you like me to show the **variant that returns `DemandeQualification`** instead of a DTO as well? (so you can have one for internal service use and one for controllers)
