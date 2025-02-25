2025-02-25 15:24:23.212 ERROR 18600 --- [nio-9090-exec-3] o.a.c.c.C.[.[.[/].[dispatcherServlet]    : Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Request processing failed; nested exception is org.springframework.dao.InvalidDataAccessApiUsageException: org.hibernate.TransientPropertyValueException: object references an unsaved transient instance - save the transient instance before flushing : com.sgma.cockpit.entities.ChargeCout.fournisseur -> com.sgma.cockpit.entities.referentiels.Fournisseur; nested exception is java.lang.IllegalStateException: org.hibernate.TransientPropertyValueException: object references an unsaved transient instance - save the transient instance before flushing : com.sgma.cockpit.entities.ChargeCout.fournisseur -> com.sgma.cockpit.entities.referentiels.Fournisseur] with root cause

org.hibernate.TransientPropertyValueException: object references an unsaved transient instance - save the transient instance before flushing : com.sgma.cockpit.entities.ChargeCout.fournisseur -> com.sgma.cockpit.entities.referentiels.Fournisseur
	at org.hibernate.engine.spi.CascadingActions$8.noCascade(CascadingActions.java:379) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.engine.internal.Cascade.cascade(Cascade.java:169) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.event.internal.AbstractSaveEventListener.cascadeBeforeSave(AbstractSaveEventListener.java:426) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.event.internal.DefaultPersistEventListener.justCascade(DefaultPersistEventListener.java:167) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.event.internal.DefaultPersistEventListener.entityIsPersistent(DefaultPersistEventListener.java:160) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.event.internal.DefaultPersistEventListener.onPersist(DefaultPersistEventListener.java:124) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.event.service.internal.EventListenerGroupImpl.fireEventOnEachListener(EventListenerGroupImpl.java:118) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
  @Override
    @Transactional
    public void updateDemande(@RequestBody DemandeDTO demandeDTO) throws PermissionException, ActionNONAuthoriseException {


        if (!userService.canDo("Modifier Demande COPRO")) {
            throw new PermissionException("ressource introuvable");
        }

        System.out.println(demandeDTO);
        Optional<Demande> optionalDemande = demandeRepository.findById(demandeDTO.getId());

        if (!optionalDemande.isPresent()) {
            throw new RuntimeException("Demande a modifié est introuvable");
        }

        if (demandeDTO.getSession() == null || demandeDTO.getSession().getId() == 0) {
            demandeDTO.setSession(null); // Set session to null if ID is 0 or session is null
        }
        Demande demande = modelMapper.map(demandeDTO, Demande.class);
        List<String> statusList = Arrays.asList("COPRO", "rejetée", "validé");

        if (statusList.contains(demande.getStatusFicheCopro())) {
            throw new ActionNONAuthoriseException("Action non authoriser");
        }
        demande.getDispositifRh().forEach(dispositifRh -> {
            dispositifRh.setDemande(demande);

            dispositifRhRepository.save(dispositifRh);

        });
      /* demande.getChargeCout().forEach(chargeCout -> {
           chargeCout.setDemande(demande);

            chargeCoutRepository.save(chargeCout);

        });*/
        if (demande.getChargeCout() != null) {
            demande.getChargeCout().removeIf(Objects::isNull);
            for (ChargeCout charge : demande.getChargeCout()) {
                if (charge.getFournisseur() == null) {
                    charge.setFournisseur(null);
                }

                charge.setDemande(demande);
                chargeCoutRepository.save(charge);
            }
        }

        Demande saveDemande = demandeRepository.save(demande);
        DemandeDTO map = modelMapper.map(saveDemande, DemandeDTO.class);
       // return map;
    }

