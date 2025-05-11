public boolean canAddRisqueInstance(Long demandeId) throws PermissionException {
    User user = userService.getAuthenticatedUser();
    String userEmail = user.getEmail();

    // Get the demande
    DemandeQualification demande = demandeQualificationRepository.findById(demandeId)
            .orElseThrow(() -> new IllegalArgumentException("Aucune demande avec id = " + demandeId));

    // Get all risques from the template
    List<Risque> allRisques = risqueRepository.findAll();

    // Filter risques where current user is the validateur
    List<Risque> risquesOfUser = allRisques.stream()
            .filter(r -> r.getValidateur() != null && r.getValidateur().getEmail().equals(userEmail))
            .toList();

    // If user is not validateur of any risque, return false
    if (risquesOfUser.isEmpty()) {
        return false;
    }

    // Get all existing RisqueInstances in this demande
    List<RisqueInstance> existingInstances = risqueInstanceRepository.findByDemandeQualificationId(demandeId);
    Set<Long> instantiatedRisqueIds = existingInstances.stream()
            .map(ri -> ri.getRisque().getId())
            .collect(Collectors.toSet());

    // If there's at least one risque the user is validateur of and hasn't yet added, return true
    for (Risque risque : risquesOfUser) {
        if (!instantiatedRisqueIds.contains(risque.getId())) {
            return true;
        }
    }

    // All risques of user already instantiated
    return false;
}
