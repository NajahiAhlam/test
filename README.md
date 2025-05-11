public boolean canAddRisqueInstance(Long demandeId) throws PermissionException {
    User user = userService.getAuthenticatedUser();
    String userEmail = user.getEmail();

    // Get all risques where the current user is the validateur
    List<Risque> allRisques = risqueRepository.findAll();
    List<Risque> userRisques = new ArrayList<>();
    for (Risque r : allRisques) {
        if (r.getValidateur() != null && r.getValidateur().getEmail().equals(userEmail)) {
            userRisques.add(r);
        }
    }

    if (userRisques.isEmpty()) {
        return false; // user is not validateur of any risque
    }

    // Get all risqueInstances of this demande
    List<RisqueInstance> existingInstances = risqueInstanceRepository.findByDemandeQualificationId(demandeId);
    Set<Long> alreadyInstantiatedRisqueIds = new HashSet<>();
    for (RisqueInstance instance : existingInstances) {
        if (instance.getRisque() != null) {
            alreadyInstantiatedRisqueIds.add(instance.getRisque().getId());
        }
    }

    // Check if there's at least one risque the user is validateur of that is not yet instantiated
    for (Risque r : userRisques) {
        if (!alreadyInstantiatedRisqueIds.contains(r.getId())) {
            return true; // this risque has not yet been added to this demande
        }
    }

    return false; // all user risques already instantiated
}
