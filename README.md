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
