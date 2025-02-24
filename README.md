if (demande.getChargeCout() != null) {
    demande.getChargeCout().removeIf(Objects::isNull); // Remove null entries
    for (ChargeCout charge : demande.getChargeCout()) {
        if (charge.getFournisseur() == null) {
            charge.setFournisseur(null); // Explicitly set to null if not provided
        }
        charge.setDemande(demande); // Ensure bidirectional relationship is set
    }
}
@OneToMany(mappedBy = "demande", cascade = CascadeType.ALL, orphanRemoval = true)
private List<ChargeCout> chargeCout;
@Transactional
public Demande updateDemande(Long id, Demande updatedDemande) {
    Optional<Demande> existingDemandeOpt = demandeRepository.findById(id);
    if (existingDemandeOpt.isEmpty()) {
        throw new ResourceNotFoundException("Demande not found with id: " + id);
    }

    Demande existingDemande = existingDemandeOpt.get();

    // Update basic fields
    existingDemande.setTitle(updatedDemande.getTitle());
    existingDemande.setDescription(updatedDemande.getDescription());
    existingDemande.setStatus(updatedDemande.getStatus());
    
    // Handle ChargeCout List
    if (updatedDemande.getChargeCout() != null) {
        // Remove existing ChargeCout related to this Demande
        chargeCoutRepository.deleteByDemande(existingDemande);

        List<ChargeCout> updatedChargeCoutList = new ArrayList<>();
        for (ChargeCout charge : updatedDemande.getChargeCout()) {
            if (charge != null) { // Avoid null entries
                charge.setDemande(existingDemande); // Ensure proper bidirectional relationship
                
                if (charge.getFournisseur() == null) {
                    charge.setFournisseur(null); // Explicitly setting null to prevent issues
                }
                
                updatedChargeCoutList.add(charge);
            }
        }

        existingDemande.setChargeCout(updatedChargeCoutList);
    }

    // Save and return updated demande
    return demandeRepository.save(existingDemande);
}
