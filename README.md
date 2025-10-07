Perfect \U0001f44d \u2014 let\u2019s go step by step and make a **production-ready, fully optimized** implementation using `processAction`.

This version will replace all those duplicated methods with **one clean, dynamic method** \u2014 easy to maintain, extend, and test.

---

## \U0001f9f1 Step 1 \u2014 Create your enum `DemandeTransition`

Put it in your domain or service package (e.g. `com.yourapp.workflow`):

```java
package com.yourapp.workflow;

import lombok.AllArgsConstructor;
import lombok.Getter;

@Getter
@AllArgsConstructor
public enum DemandeTransition {
    SUSPENDRE(WfStatus.SUSPENDU, "suspendu", UserAction.SUSPENDRE, WfStatus.CLOTURE_POSTE_CONDITION),
    REOUVERT(WfStatus.CLOTURE_POSTE_CONDITION, "reouvrir", UserAction.REOUVERT, WfStatus.SUSPENDU),
    ANNULER(WfStatus.ANNULER, "annuler", UserAction.ANNULER, WfStatus.CNP_A_PLANIFIER),
    EXPIRE(WfStatus.EXPIRE, "expire", UserAction.EXPIRE, null),
    PRE_CNP(WfStatus.PRE_CNP_A_PLANIFIER, "precnp", UserAction.PRE_CNP_A_PLANIFIER, WfStatus.ANALYSE_RISQUE);

    private final WfStatus newStatus;
    private final String actionKey;
    private final UserAction userAction;
    private final WfStatus previousStatus;
}
```

\u2705 **Why**
This enum centralizes all transition metadata in one place \u2014 no more hardcoded strings or status scattered across methods.

---

## \u2699\ufe0f Step 2 \u2014 Add the `processAction` method in your service

Inside your service (e.g. `DemandeQualificationService`), replace all the old methods with this single one:

```java
public DemandeQualificationResponse processAction(Long id, String actionName) throws PermissionException {
    // Step 1: Match the actionName to an enum entry
    DemandeTransition transition = Arrays.stream(DemandeTransition.values())
            .filter(t -> t.getActionKey().equalsIgnoreCase(actionName))
            .findFirst()
            .orElseThrow(() -> new IllegalArgumentException("Action inconnue : " + actionName));

    // Step 2: Load the demande
    DemandeQualification demande = demandeQualificationRepository.findById(id)
            .orElseThrow(() -> new IllegalArgumentException("Aucune demande avec id = " + id));

    // Step 3: Prepare parameters for the workflow
    Map<String, String> params = createSubmitParam2(demande, transition.getNewStatus(), transition.getActionKey());
    ActivateProcessResult submit = submit(demande, params);

    // Step 4: Update demande status
    updateDemandeStatus(demande, transition.getNewStatus(), submit);
    demandeQualificationRepository.save(demande);

    // Step 5: Determine previous status (auto if null)
    WfStatus previousStatus = transition.getPreviousStatus() != null
            ? transition.getPreviousStatus()
            : WfStatus.valueOf(demande.getStatus());

    // Step 6: Log history
    demandeHistoryService.bpmnDemandeHistory(
            demande,
            submit,
            transition.getUserAction(),
            previousStatus,
            transition.getNewStatus()
    );

    // Step 7: Return DTO response
    return new DemandeQualificationResponseMapper().mapToDTO(demande);
}
```

\u2705 **Why**
This single method:

* Finds which transition to use based on the action name
* Applies the workflow logic
* Saves and logs the change
* Returns the same DTO you already use

---

## \U0001fa84 Step 3 \u2014 Replace all old methods

Remove `suspenduRequest`, `reouvertRequest`, `annulerRequest`, `expireRequest`, and `movePreCnp`.

They are now replaced by *one universal method*.

If you still want backward-compatible wrappers for controllers, you can keep:

```java
public DemandeQualificationResponse suspenduRequest(Long id) throws PermissionException {
    return processAction(id, "suspendu");
}
```

\u2026but that\u2019s optional.

---

## \U0001f3af Step 4 \u2014 Use it in your controller or service calls

In your controller, you can now expose one API:

```java
@PostMapping("/{id}/action/{actionName}")
public ResponseEntity<DemandeQualificationResponse> processAction(
        @PathVariable Long id,
        @PathVariable String actionName
) throws PermissionException {
    DemandeQualificationResponse response = demandeQualificationService.processAction(id, actionName);
    return ResponseEntity.ok(response);
}
```

Example calls:

```
POST /demande/123/action/suspendu
POST /demande/123/action/reouvrir
POST /demande/123/action/annuler
POST /demande/123/action/precnp
POST /demande/123/action/expire
```

---

## \U0001f9e9 Step 5 \u2014 Add a safeguard (optional but recommended)

To make it robust, ensure only allowed actions are passed:

```java
if (!EnumUtils.isValidEnumIgnoreCase(DemandeTransition.class, actionName)) {
    throw new IllegalArgumentException("Action non supportée: " + actionName);
}
```

Or just rely on the enum filter above (already safe).

---

## \u2705 Final Summary

| Component                      | Description                                         |
| ------------------------------ | --------------------------------------------------- |
| **Enum `DemandeTransition`**   | Defines all workflow transitions in one place       |
| **`processAction()` method**   | Dynamically executes the right transition           |
| **Single Controller endpoint** | `/demande/{id}/action/{actionName}` for all actions |
| **Zero duplicate code**        | No more repeated repository logic                   |
| **Easily extendable**          | Add a new transition \u2192 just add one enum constant   |

---

Would you like me to show how to inject and reuse the `DemandeQualificationResponseMapper` via Spring (so you don\u2019t `new` it each time)? That\u2019s a micro-optimization for cleaner DI.
