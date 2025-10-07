Excellent \u2014 yes \U0001f44f you\u2019re totally right.
All these methods share **exactly the same logic**, only differing in:

* the **new status** (`WfStatus`) you want to set,
* the **action key** you pass to `createSubmitParam2`,
* the **user action** (`UserAction` enum), and
* the **previous status** (for history logging).

We can easily refactor this into a **generic reusable method**, and your service becomes *much cleaner* and *less error-prone*.

---

### \u2705 Step 1. Create a helper method

Add a **private utility method** that performs the shared logic:

```java
private DemandeQualificationResponse processDemandeStatusChange(
        Long demandeId,
        WfStatus newStatus,
        String actionKey,
        UserAction userAction,
        WfStatus previousStatus
) throws PermissionException {
    DemandeQualification demande = demandeQualificationRepository.findById(demandeId)
            .orElseThrow(() -> new IllegalArgumentException("Aucune demande avec id = " + demandeId));

    Map<String, String> submitParam = createSubmitParam2(demande, newStatus, actionKey);
    ActivateProcessResult submit = submit(demande, submitParam);

    updateDemandeStatus(demande, newStatus, submit);

    DemandeQualification saved = demandeQualificationRepository.save(demande);

    demandeHistoryService.bpmnDemandeHistory(saved, submit, userAction, previousStatus, newStatus);

    return new DemandeQualificationResponseMapper().mapToDTO(saved);
}
```

---

### \u2705 Step 2. Simplify all your methods

Now each of your public methods becomes *a one-liner*:

```java
public DemandeQualificationResponse suspenduRequest(Long id) throws PermissionException {
    return processDemandeStatusChange(
            id,
            WfStatus.SUSPENDU,
            "suspendu",
            UserAction.SUSPENDRE,
            WfStatus.CLOTURE_POSTE_CONDITION
    );
}

public DemandeQualificationResponse reouvertRequest(Long id) throws PermissionException {
    return processDemandeStatusChange(
            id,
            WfStatus.CLOTURE_POSTE_CONDITION,
            "reouvrir",
            UserAction.REOUVERT,
            WfStatus.SUSPENDU
    );
}

public DemandeQualificationResponse annulerRequest(Long id) throws PermissionException {
    return processDemandeStatusChange(
            id,
            WfStatus.ANNULER,
            "annuler",
            UserAction.ANNULER,
            WfStatus.CNP_A_PLANIFIER
    );
}

public DemandeQualificationResponse expireRequest(Long id) throws PermissionException {
    return processDemandeStatusChange(
            id,
            WfStatus.EXPIRE,
            "expire",
            UserAction.EXPIRE,
            WfStatus.valueOf(demandeQualificationRepository.findById(id)
                    .orElseThrow(() -> new IllegalArgumentException("Aucune demande avec id = " + id))
                    .getStatus())
    );
}

public DemandeQualificationResponse movePreCnp(Long id) throws PermissionException {
    return processDemandeStatusChange(
            id,
            WfStatus.PRE_CNP_A_PLANIFIER,
            "precnp",
            UserAction.PRE_CNP_A_PLANIFIER,
            WfStatus.ANALYSE_RISQUE
    );
}
```

---

### \u2705 Step 3. Optional \u2014 use an Enum to make it even cleaner

If these transitions are all known and fixed, you can define an enum that encapsulates the parameters:

```java
public enum DemandeTransition {
    SUSPENDRE(WfStatus.SUSPENDU, "suspendu", UserAction.SUSPENDRE, WfStatus.CLOTURE_POSTE_CONDITION),
    REOUVERT(WfStatus.CLOTURE_POSTE_CONDITION, "reouvrir", UserAction.REOUVERT, WfStatus.SUSPENDU),
    ANNULER(WfStatus.ANNULER, "annuler", UserAction.ANNULER, WfStatus.CNP_A_PLANIFIER),
    EXPIRE(WfStatus.EXPIRE, "expire", UserAction.EXPIRE, null),
    PRE_CNP(WfStatus.PRE_CNP_A_PLANIFIER, "precnp", UserAction.PRE_CNP_A_PLANIFIER, WfStatus.ANALYSE_RISQUE);

    public final WfStatus newStatus;
    public final String actionKey;
    public final UserAction userAction;
    public final WfStatus previousStatus;

    DemandeTransition(WfStatus newStatus, String actionKey, UserAction userAction, WfStatus previousStatus) {
        this.newStatus = newStatus;
        this.actionKey = actionKey;
        this.userAction = userAction;
        this.previousStatus = previousStatus;
    }
}
```

Then your method could look like this:

```java
public DemandeQualificationResponse handleTransition(Long id, DemandeTransition transition) throws PermissionException {
    return processDemandeStatusChange(
            id,
            transition.newStatus,
            transition.actionKey,
            transition.userAction,
            transition.previousStatus
    );
}
```

So your calls become simple and clean:

```java
public DemandeQualificationResponse suspenduRequest(Long id) throws PermissionException {
    return handleTransition(id, DemandeTransition.SUSPENDRE);
}
```

---

### \u2705 Summary of Benefits

* **Single point of logic** \u2192 easier maintenance
* **No code repetition** \u2192 fewer mistakes
* **Clearer mapping between status transitions and actions**
* Easy to **extend** by adding new transitions to the enum

---

Would you like me to make the enum-based version fully dynamic (auto-detecting previous status from the entity instead of passing it)? That would make it even cleaner.
