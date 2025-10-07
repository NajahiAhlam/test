Excellent question \U0001f4aa \u2014 you\u2019re moving in the right direction with `processAction`, but when your logic involves **extra business treatment** (like file upload, date parsing, or setting fields before saving), you need a **flexible and clean way** to plug that logic into the generic process.

Let\u2019s go step-by-step \U0001f447

---

## \U0001f9e0 Goal

You want:
\u2705 One unified `processAction(...)` method
\u2705 Support for actions like *kickoff*, *annuler*, *suspendu*, etc.
\u2705 Some actions having **custom logic** (e.g. file upload, set date, decision, etc.)

---

## \U0001f9e9 Step 1 \u2014 Make `processAction` generic

Your unified method will receive:

* `Long demandeId`
* `String actionKey`
* Optional `MultipartFile attachment`
* Optional `Map<String, Object> extraParams` for custom logic (like `date`, `decision`, etc.)

### Example

```java
public DemandeQualificationResponse processAction(
        Long demandeId,
        String actionKey,
        MultipartFile attachment,
        Map<String, Object> extraParams
) throws PermissionException, IOException {

    DemandeQualification demande = demandeQualificationRepository.findById(demandeId)
            .orElseThrow(() -> new IllegalArgumentException("Aucune demande avec id = " + demandeId));

    // 1. Resolve the action details (config-driven)
    ProcessActionConfig actionConfig = ProcessActionConfig.fromKey(actionKey);
    WfStatus newStatus = actionConfig.getNewStatus();

    // 2. Submit BPM task
    Map<String, String> submitParam = createSubmitParam2(demande, newStatus, actionKey);
    ActivateProcessResult submit = submit(demande, submitParam);

    // 3. Run pre-save business logic (custom per action)
    handleCustomActionLogic(demande, actionKey, attachment, extraParams);

    // 4. Update and save
    updateDemandeStatus(demande, newStatus, submit);
    DemandeQualification saved = demandeQualificationRepository.save(demande);

    // 5. History
    demandeHistoryService.bpmnDemandeHistory(saved, submit,
            actionConfig.getUserAction(),
            actionConfig.getPreviousStatus(),
            newStatus);

    // 6. Map response
    return new DemandeQualificationResponseMapper().mapToDTO(saved);
}
```

---

## \U0001f9e9 Step 2 \u2014 Define `ProcessActionConfig`

A small enum or registry that maps each `actionKey` to its workflow configuration:

```java
public enum ProcessActionConfig {
    ANNULER("annuler", WfStatus.ANNULER, UserAction.ANNULER, WfStatus.CNP_A_PLANIFIER),
    SUSPENDU("suspendu", WfStatus.SUSPENDU, UserAction.SUSPENDRE, WfStatus.CLOTURE_POSTE_CONDITION),
    REOUVERT("reouvrir", WfStatus.CLOTURE_POSTE_CONDITION, UserAction.REOUVERT, WfStatus.SUSPENDU),
    PRE_CNP("precnp", WfStatus.PRE_CNP_A_PLANIFIER, UserAction.PRE_CNP_A_PLANIFIER, WfStatus.ANALYSE_RISQUE),
    KICKOFF("kickoff", WfStatus.MAJ_VALIDATION_QUESTIONNAIRE, UserAction.SOUMETTRE_LEAD, WfStatus.KICK_OFF);

    private final String key;
    private final WfStatus newStatus;
    private final UserAction userAction;
    private final WfStatus previousStatus;

    ProcessActionConfig(String key, WfStatus newStatus, UserAction userAction, WfStatus previousStatus) {
        this.key = key;
        this.newStatus = newStatus;
        this.userAction = userAction;
        this.previousStatus = previousStatus;
    }

    public static ProcessActionConfig fromKey(String key) {
        return Arrays.stream(values())
                .filter(a -> a.key.equalsIgnoreCase(key))
                .findFirst()
                .orElseThrow(() -> new IllegalArgumentException("Unknown action key: " + key));
    }

    public WfStatus getNewStatus() { return newStatus; }
    public WfStatus getPreviousStatus() { return previousStatus; }
    public UserAction getUserAction() { return userAction; }
}
```

---

## \U0001f9e9 Step 3 \u2014 Implement `handleCustomActionLogic`

This is where you handle \u201cspecial\u201d actions like `KickoffRequest` cleanly without duplicating logic.

```java
private void handleCustomActionLogic(DemandeQualification demande,
                                     String actionKey,
                                     MultipartFile attachment,
                                     Map<String, Object> extraParams) throws IOException {

    if ("kickoff".equalsIgnoreCase(actionKey)) {
        // 1. Parse extra fields
        String dateStr = (String) extraParams.get("date");
        String decision = (String) extraParams.get("decision");

        if (dateStr != null) {
            LocalDate localDate = LocalDate.parse(dateStr);
            Date dateKickoff = Date.from(localDate.atStartOfDay(ZoneId.systemDefault()).toInstant());
            demande.setDateKickOff(dateKickoff);
        }

        demande.setKickOffDecision(decision);

        // 2. Handle attachment
        if (attachment != null && !attachment.isEmpty()) {
            attachmentService.saveAttachment(attachment, demande.getId(), "demande", null);
        }
    }

    // Add future actions easily here:
    // else if ("expire".equalsIgnoreCase(actionKey)) { ... }
}
```

---

## \U0001f9e9 Step 4 \u2014 Frontend Example Call

When calling from Angular for kickoff:

```typescript
const formData = new FormData();
formData.append('actionKey', 'kickoff');
formData.append('date', this.form.value.date);
formData.append('decision', this.form.value.decision);
formData.append('attachment', this.selectedFile);

this.incidentService.processAction(this.incidentId, formData).subscribe(...);
```

---

## \u2705 Benefits of this refactor

| \u2705                                            | Description                                                                 |
| -------------------------------------------- | --------------------------------------------------------------------------- |
| **One unified endpoint**                     | No more 5 different controller methods                                      |
| **Easy to extend**                           | Add new actions only in `ProcessActionConfig` and `handleCustomActionLogic` |
| **Handles attachments & params dynamically** | Works for both simple and complex actions                                   |
| **Clean backend**                            | Centralized workflow and history handling                                   |

---

If you show me your current `processAction` controller signature and frontend call format (is it `FormData` or JSON?),
I can give you **the exact updated controller + Angular service version** to handle attachments properly.
Would you like me to do that?
