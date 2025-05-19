public List<Risque> getRisquesNonUtilisesDansDemande(Long demandeId) {
    // 1. Récupérer la demande avec ses instances de risques
    DemandeQualification demande = demandeQualificationRepository.findById(demandeId)
        .orElseThrow(() -> new RuntimeException("Demande introuvable"));

    // 2. Obtenir tous les risques existants
    List<Risque> tousLesRisques = risqueRepository.findAll();

    // 3. Extraire les risques déjà liés à cette demande via une RisqueInstance
    Set<Long> risquesLiesIds = demande.getRisqueInstances().stream()
        .map(ri -> ri.getRisque().getId())
        .collect(Collectors.toSet());

    // 4. Filtrer les risques qui ne sont pas dans la liste des liés
    return tousLesRisques.stream()
        .filter(risque -> !risquesLiesIds.contains(risque.getId()))
        .collect(Collectors.toList());
}
