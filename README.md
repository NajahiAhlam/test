public boolean canAddRisqueInstance(Long demandeId) throws PermissionException {
    User user = userService.getAuthenticatedUser();
    String userEmail = user.getEmail();

    // Get all risques where the current user is validateur
    List<Risque> risquesOfUser = new ArrayList<>();
    for (Risque r : risqueRepository.findAll()) {
        if (r.getValidateur() != null && r.getValidateur().getEmail().equals(userEmail)) {
            risquesOfUser.add(r);
        }
    }

    // If the user is not validateur of any risque → return false
    if (risquesOfUser.isEmpty()) {
        return false;
    }

    // Get all risqueInstances already linked to the demande
    List<RisqueInstance> existingInstances = risqueInstanceRepository.findByDemandeQualificationId(demandeId);
    Set<Long> instantiatedRisqueIds = new HashSet<>();
    for (RisqueInstance instance : existingInstances) {
        if (instance.getRisque() != null) {
            instantiatedRisqueIds.add(instance.getRisque().getId());
        }
    }

    // Check if there’s any risque (where user is validateur) that has NOT been instantiated
    for (Risque r : risquesOfUser) {
        if (!instantiatedRisqueIds.contains(r.getId())) {
            return true; // user is validateur of a risque not yet added as instance
        }
    }

    // All risques of the user already instantiated, or user has none → return false
    return false;
}
