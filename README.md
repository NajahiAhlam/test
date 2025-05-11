   public boolean hasUserCreatedRisqueInstanceAsValidateur(Long demandeId) throws PermissionException {
        User user= userService.getAuthenticatedUser();
        String userEmail = user.getEmail();
        // Get all RisqueInstances linked to the demande
        List<RisqueInstance> instances = risqueInstanceRepository.findByDemandeQualificationId(demandeId);

        for (RisqueInstance instance : instances) {
            Risque risque = instance.getRisque();
            if (risque != null && risque.getValidateur() != null &&
                    risque.getValidateur().getEmail().equals(userEmail)) {
                return true;
            }
        }
        
        
        return false;
    }
i want to return true if the user connect is validateur of risque and not yet ajout the risque so i display the button and can add the risqueinstance
