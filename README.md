public boolean hasUserCreatedRisqueInstanceAsValidateur(String userEmail, Long demandeId) {
    // Get all RisqueInstances linked to the demande
    List<RisqueInstance> instances = risqueInstanceRepository.findByDemandeId(demandeId);

    for (RisqueInstance instance : instances) {
        Risque risque = instance.getRisque();
        if (risque != null && risque.getValidateur() != null &&
            risque.getValidateur().getEmail().equals(userEmail)) {
            return true;
        }
    }
    return false;
}
