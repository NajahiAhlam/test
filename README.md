@Transactional
public RisqueInstance validerRisqueInstance(Long idRisque, List<Conditions> conditions, String comment, String typeValidation) {
    RisqueInstance risqueInstance = risqueInstanceRepository.findById(idRisque)
            .orElseThrow(() -> new EntityNotFoundException("RisqueInstance not found with id: " + idRisque));

    boolean hasConditions = conditions != null && !conditions.isEmpty();
    boolean hasComment = comment != null && !comment.isBlank();

    if (hasConditions) {
        for (Conditions condition : conditions) {
            condition.setDateLancement(LocalDate.now()); // Optional: you can customize defaults
            condition.setEtat("EN_ATTENTE"); // Optional default state
        }

        // Link to parent
        conditions.forEach(c -> c.setRisqueInstance(risqueInstance)); // If bidirectional
        risqueInstance.setConditions(conditions);
    } else if (hasComment) {
        risqueInstance.setComment(comment);
    }

    if (hasConditions || hasComment) {
        risqueInstance.setDateValidation(LocalDateTime.now());
        risqueInstance.setValider(true);
        risqueInstance.setTypeValidation(typeValidation);
    }

    return risqueInstanceRepository.save(risqueInstance);
}
public class RisqueValidationRequest {
    private List<Conditions> conditions;
    private String comment;
    private String typeValidation;
}
@PostMapping("/risques/{id}/valider")
public ResponseEntity<RisqueInstance> validerRisque(
        @PathVariable Long id,
        @RequestBody RisqueValidationRequest request) {

    RisqueInstance updated = service.validerRisqueInstance(
        id, request.getConditions(), request.getComment(), request.getTypeValidation()
    );
    return ResponseEntity.ok(updated);
}
  validerRisque(id: number, payload: RisqueValidationRequest): Observable<RisqueInstance> {
    return this.http.post<RisqueInstance>(`${this.apiUrl}/${id}/valider`, payload);
  }
  submit() {
  const formValue = this.form.value;

  const payload: RisqueValidationRequest = {
    conditions: formValue.conditions && formValue.conditions.length > 0 ? formValue.conditions : [],
    comment: formValue.comment,
    typeValidation: formValue.validation
  };

  this.risqueInstanceService.validerRisque(this.risqueId, payload).subscribe({
    next: (updatedRisque) => {
      console.log('Risque validé :', updatedRisque);
      // Afficher toast ou naviguer
    },
    error: (err) => {
      console.error('Erreur validation risque :', err);
      // Afficher message d'erreur
    }
  });
}
