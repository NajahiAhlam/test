  public String convertToProjet(List<ProjetDTO> projetDTOList) throws SsoException {
        if (!userService.canDo("Convertir Initiative")) {
            throw new SsoException("Ressource introuvable");
        }

        List<Projet> projects = projetDTOList.stream()
                .map(this::convertDTOToProjet)
                .collect(Collectors.toList());

        projetRepository.saveAll(projects);

        return "The projet converted successfully";
    }
 
    private Projet convertDTOToProjet(ProjetDTO dto) {
        Projet projet = modelMapper.map(dto, Projet.class);
        setCategoriess(projet, dto.getCategoriesIds());
        updateProjetFields(projet);
        cloneBudgets(projet);
        logConversionHistory(projet);
        return projet;
    }

    private void setCategoriess(Projet projet, List<Long> categoryIds) {
        if (categoryIds != null && !categoryIds.isEmpty() ) {
            List<Categorie> categories = categorieRepository.findAllById(categoryIds);
            projet.setCategories(categories);
        }
        else {
            projet.setCategories(Collections.emptyList());
        }
    }
     private void updateProjetFields(Projet projet) {
        projet.setInitiative(false);
        if (projet.getRefProjet() != null) {
            String phase = getPhaseFromRefProjet(projet.getRefProjet());
            if (phase != null) {
                projet.setPhase(phase);
            }
        } else {
            projet.setPhase("Opportunité");
        }
        projet.setDateConvertion(LocalDateTime.now());
    }
    private String getPhaseFromRefProjet(Long refProjetId) {
        if (refProjetId != null) {
            Projet refProjet = projetRepository.findById(refProjetId).orElse(null);
            if (refProjet != null) {
                return refProjet.getPhase();
            }
        }
        return null;
    }

    private void cloneBudgets(Projet projet) {
        if (projet.getLeadPrincipal() != null) {
            cloneBudget(projet.getLeadPrincipal().getBudgetInitial(), projet, projet.getLeadPrincipal());
        }

        if (projet.getLeadContributeurs() != null && !projet.getLeadContributeurs().isEmpty()) {
            for (Lead leadContributeur : projet.getLeadContributeurs()) {
                cloneBudget(leadContributeur.getBudgetInitial(), projet, leadContributeur);
            }
        }
    }

    private void cloneBudget(Budget budgetInitial, Projet projet, Lead lead) {
        if (budgetInitial != null) {
            BudgetRevise budgetRevise = new BudgetRevise();
            budgetRevise.setIntern(budgetInitial.getIntern());
            budgetRevise.setExtern(budgetInitial.getExtern());
            budgetRevise.setProjetId(projet.getId());
            budgetRevise.setLead(lead);
            budgetReviseRepository.save(budgetRevise);

            if (lead != null) {
                lead.setBudgetRevises(Collections.singletonList(budgetRevise));
            }
        }
    }

    private void logConversionHistory(Projet projet) {
        try {
            createHistory("Convert", "Initiative", projet.getPhase(), Action.CONVERTIR, projet, getAuthenticatedUserEmail());
        } catch (SsoException e) {
            throw new RuntimeException("Failed to create history", e);
        }
    }
      public ObjectPagination<ProjetDTO> findAllInitiatives(int page, int size, String sortDirection, String sortValue, Map<String, String> filters) throws SsoException {
       if (userService.canDo("Lire Initiative")) {
           Specification<Projet> spec = (root, query, criteriaBuilder) -> {
               List<Predicate> predicates = new ArrayList<>();

               predicates.add(criteriaBuilder.isTrue(root.get("isInitiative")));

               List<String> validFilterKeys = Arrays.asList("phase", "nom", "enjeux", "categorie", "typeInvestissement", "priorite", "natureProgramme", "programme", "leadPrincipal", "refSciforma", "fdr", "waveN", "typeAgile", "dateLancement", "dateCloture", "dateCopro1", "dateCopro2", "dateCopro3", "risques", "gainsQuantitatifs", "gainsQualitatifs");

               filters.forEach((key, value) -> {
                   if (!validFilterKeys.contains(key)) {
                       return;
                   }

                   switch (key) {
                       case "leadPrincipal":
                           predicates.add(criteriaBuilder.like(root.get("leadPrincipal").get("intitule"), "%" + value + "%"));
                           break;
                       case "phase":
                           predicates.add(criteriaBuilder.like(root.get("phase"), "%" + value + "%"));
                           break;
                       case ENABLED:
                           if (Boolean.parseBoolean(value)) {
                               predicates.add(criteriaBuilder.isTrue(root.get(ENABLED)));
                           } else {
                               predicates.add(criteriaBuilder.isFalse(root.get(ENABLED)));
                           }
                           break;
                       case "nom":
                           predicates.add(criteriaBuilder.like(root.get("nom"), "%" + value + "%"));
                           break;
                       case "enjeux":
                           predicates.add(criteriaBuilder.like(root.get("enjeux").get("nom"), "%" + value + "%"));
                           break;
                       case "categorie":
                           predicates.add(criteriaBuilder.like(root.get("categorie").get("nom"), "%" + value + "%"));
                           break;
                       case "typeInvestissement":
                           predicates.add(criteriaBuilder.like(root.get("typeInvestissement"), "%" + value + "%"));
                           break;
                       case "priorite":
                           predicates.add(criteriaBuilder.like(root.get("priorite"), "%" + value + "%"));
                           break;
                       case "natureProgramme":
                           predicates.add(criteriaBuilder.like(root.get("natureProgramme").get("nom"), "%" + value + "%"));
                           break;
                       case "programme":
                           predicates.add(criteriaBuilder.like(root.get("programme").get("nom"), "%" + value + "%"));
                           break;


                       case "refSciforma":
                           predicates.add(criteriaBuilder.like(root.get("refSciforma"), "%" + value + "%"));
                           break;
                       case "fdr":
                           predicates.add(criteriaBuilder.equal(root.get("fdr").get("annee"), Integer.parseInt(value)));
                           break;
                       case "waveN":
                           predicates.add(criteriaBuilder.like(root.get("waveN"), "%" + value + "%"));
                           break;
                       case "typeAgile":
                           predicates.add(criteriaBuilder.like(root.get("typeAgile").get("nom"), "%" + value + "%"));
                           break;
                       case "dateLancement":
                           predicates.add(criteriaBuilder.between(root.get("dateLancement"), parseDate(value, "startDate"), parseDate(value, "endDate")));
                           break;
                       case "dateCloture":
                           predicates.add(criteriaBuilder.between(root.get("dateCloture"), parseDate(value, "startDate"), parseDate(value, "endDate")));
                           break;
                       case "dateCopro1":
                           predicates.add(criteriaBuilder.between(root.get("dateCopro1"), parseDate(value, "startDate"), parseDate(value, "endDate")));
                           break;
                       case "dateCopro2":
                           predicates.add(criteriaBuilder.between(root.get("dateCopro2"), parseDate(value, "startDate"), parseDate(value, "endDate")));
                           break;
                       case "dateCopro":
                           predicates.add(criteriaBuilder.between(root.get("dateCopro3"), parseDate(value, "startDate"), parseDate(value, "endDate")));
                           break;
                       case "risques":
                           predicates.add(criteriaBuilder.like(root.get("risques"), "%" + value + "%"));
                           break;
                       case "gainsQuantitatifs":
                           predicates.add(criteriaBuilder.like(root.get("gainsQuantitatifs"), "%" + value + "%"));
                           break;
                       case "gainsQualitatifs":
                           predicates.add(criteriaBuilder.like(root.get("gainsQualitatifs"), "%" + value + "%"));
                           break;
                   }
               });

               return criteriaBuilder.and(predicates.toArray(new Predicate[0]));
           };

           Sort sort = Sort.by(Objects.equals(sortDirection, "ASC") ? Sort.Direction.ASC : Sort.Direction.DESC, sortValue);

           Page<Projet> pageOfInitiative = projetRepository.findAll(spec, PageRequest.of(page, size, sort));
           ObjectPagination<ProjetDTO> pagination = new ObjectPagination<>();
           pagination.setContent(pageOfInitiative.stream()
                   .map(initiative -> modelMapper.map(initiative, ProjetDTO.class))
                   .collect(Collectors.toList()));
           pagination.setLast(pageOfInitiative.isLast());
           pagination.setFirst(pageOfInitiative.isFirst());
           pagination.setTotalElements((int) pageOfInitiative.getTotalElements());
           pagination.setTotalPages(pageOfInitiative.getTotalPages());
           pagination.setSize(pageOfInitiative.getSize());
           pagination.setNumber(pageOfInitiative.getNumber());

           return pagination;
       }
       throw new SsoException("Ressource introuvable");
   }
