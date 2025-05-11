public boolean canAddRisqueInstance(Long demandeId) throws PermissionException {
    User user = userService.getAuthenticatedUser();
    String userEmail = user.getEmail();

    // Get the demande
    DemandeQualification demande = demandeQualificationRepository.findById(demandeId)
            .orElseThrow(() -> new IllegalArgumentException("Aucune demande avec id = " + demandeId));

    // Get all risques
    List<Risque> allRisques = risqueRepository.findAll();

    // Filter risques where the current user is the validateur
    List<Risque> risquesOfUser = new ArrayList<>();
    for (Risque r : allRisques) {
        if (r.getValidateur() != null && r.getValidateur().getEmail().equals(userEmail)) {
            risquesOfUser.add(r);
        }
    }

    // If user is not a validateur of any risque, return false
    if (risquesOfUser.isEmpty()) {
        return false;
    }

    // Get all existing RisqueInstances in this demande
    List<RisqueInstance> existingInstances = risqueInstanceRepository.findByDemandeQualificationId(demandeId);
    Set<Long> instantiatedRisqueIds = new HashSet<>();
    for (RisqueInstance ri : existingInstances) {
        if (ri.getRisque() != null) {
            instantiatedRisqueIds.add(ri.getRisque().getId());
        }
    }

    // If there's at least one risque (where user is validateur) that hasn't been instantiated yet → return true
    for (Risque risque : risquesOfUser) {
        if (!instantiatedRisqueIds.contains(risque.getId())) {
            return true;
        }
    }

    // All risques for this user have already been instantiated → return false
    return false;
}
