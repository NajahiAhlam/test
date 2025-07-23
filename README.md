 @Transactional
    public ObjectPagination<DemandeRequestDTO> findAlldemandes(int page, int size, String sortDirection, String sortValue, Map<String, String> filters) throws PermissionException {
        User currentUser = userService.getAuthenticatedUser();

        Specification<DemandeQualification> spec = (root, query, criteriaBuilder) -> {
            List<Predicate> predicates = new ArrayList<>();

            if (userService.hasRole(currentUser, Role.Leader)) {
                predicates.add(criteriaBuilder.equal(root.get("reporter").get("id"), currentUser.getId()));
            } else if (userService.hasRole(currentUser, Role.SponsorM)) {
                predicates.add(criteriaBuilder.equal(root.get("sponsorM").get("id"), currentUser.getId()));
            }

            predicates.add(criteriaBuilder.notEqual(
                    criteriaBuilder.lower(root.get("status")),
                    "non_eligible"
            ));

            List<String> validFilterKeys = Arrays.asList(
                    "objetDemande", "nomFiliale", "nom", "description", "porteurProjet", "porteurMetier",
                    "status", "type", DATE_KICKOFF, "kickOffDecision",
                    DATE_LANCEMENT, "dateModification", "dateCreation", "typeDemande"
            );

            filters.forEach((key, value) -> {
                if (!validFilterKeys.contains(key) || value == null || value.trim().isEmpty()) return;

                switch (key) {
                    case "objetDemande":
                    case "nomFiliale":
                    case "type":
                    case "description":
                        predicates.add(criteriaBuilder.like(criteriaBuilder.lower(root.get(key)), "%" + value.toLowerCase() + "%"));
                        break;


                    case "porteurProjet":
                        predicates.add(criteriaBuilder.like(criteriaBuilder.lower(root.get("porteurProjet").get("nom")), "%" + value.toLowerCase() + "%"));
                        break;

                    case "porteurMetier":
                        predicates.add(criteriaBuilder.like(criteriaBuilder.lower(root.get("porteurMetier").get("nom")), "%" + value.toLowerCase() + "%"));
                        break;

                    case "status":
                        predicates.add(criteriaBuilder.like(criteriaBuilder.lower(root.get("status")), "%" + value.toLowerCase() + "%"));
                        break;

                    case "typeDemande":
                        predicates.add(criteriaBuilder.like(criteriaBuilder.lower(root.get("typeDemande")), "%" + value.toLowerCase() + "%"));
                        break;

                    case "kickOffDecision":
                        predicates.add(criteriaBuilder.like(criteriaBuilder.lower(root.get("kickOffDecision")), "%" + value.toLowerCase() + "%"));
                        break;
                    case "dateCreation":
                        String processed = value.substring(4, value.indexOf("GMT")).trim();
                        DateTimeFormatter df = DateTimeFormatter.ofPattern("MMM dd yyyy HH:mm:ss", Locale.ENGLISH);
                        LocalDateTime dateC = LocalDateTime.parse(processed, df);
                        LocalDateTime startOfDayC = dateC.toLocalDate().atStartOfDay();
                        LocalDateTime endOfDate = dateC.plusDays(1).minusNanos(1);
                        predicates.add(criteriaBuilder.between(root.get("dateCreation"), startOfDayC, endOfDate));
                        break;
                    case "dateModification":
                        String processed1 = value.substring(4, value.indexOf("GMT")).trim();
                        DateTimeFormatter df1 = DateTimeFormatter.ofPattern("MMM dd yyyy HH:mm:ss", Locale.ENGLISH);
                        LocalDateTime date1 = LocalDateTime.parse(processed1, df1);
                        LocalDateTime startOfDay1 = date1.toLocalDate().atStartOfDay();
                        LocalDateTime endOfDate1 = date1.plusDays(1).minusNanos(1);
                        predicates.add(criteriaBuilder.between(root.get("dateModification"), startOfDay1, endOfDate1));
                        break;

                    case DATE_LANCEMENT:
                        String processedL = value.substring(4, value.indexOf("GMT")).trim();
                        DateTimeFormatter dfL = DateTimeFormatter.ofPattern("MMM dd yyyy HH:mm:ss", Locale.ENGLISH);
                        LocalDate dateL = LocalDate.parse(processedL, dfL);
                        LocalDate startOfDayL = dateL;
                        LocalDate endOfDateL = dateL.plusDays(1);
                        predicates.add(criteriaBuilder.between(root.get(DATE_LANCEMENT), startOfDayL, endOfDateL));
                        break;

                    case DATE_KICKOFF:
                        String processed1K = value.substring(4, value.indexOf("GMT")).trim();
                        DateTimeFormatter df1K = DateTimeFormatter.ofPattern("MMM dd yyyy HH:mm:ss", Locale.ENGLISH);
                        LocalDateTime date1K = LocalDateTime.parse(processed1K, df1K);
                        Date startOfDay1K = java.sql.Timestamp.valueOf(date1K.toLocalDate().atStartOfDay());
                        Date endOfDate1K = java.sql.Timestamp.valueOf(date1K.toLocalDate().plusDays(1).atStartOfDay());

                        predicates.add(criteriaBuilder.between(root.get(DATE_KICKOFF), startOfDay1K, endOfDate1K));
                        break;


                }
            });

            return criteriaBuilder.and(predicates.toArray(new Predicate[0]));
        };

        Sort sort = Sort.by(
                sortDirection != null && sortDirection.equalsIgnoreCase("ASC") ? Sort.Direction.ASC : Sort.Direction.DESC,
                sortValue != null ? sortValue : "dateCreation"
        );

        PageRequest pageRequest = PageRequest.of(page, size, sort);

        Page<DemandeQualification> pageOfDemande = demandeQualificationRepository.findAll(spec, pageRequest);

        ObjectPagination<DemandeRequestDTO> pagination = new ObjectPagination<>();
        pagination.setContent(pageOfDemande.getContent().stream().map(DemandeRequestMapper::mapToDTO).collect(Collectors.toList()));
        pagination.setLast(pageOfDemande.isLast());
        pagination.setFirst(pageOfDemande.isFirst());
        pagination.setTotalElements((int) pageOfDemande.getTotalElements());
        pagination.setTotalPages(pageOfDemande.getTotalPages());
        pagination.setSize(pageOfDemande.getSize());
        pagination.setNumber(pageOfDemande.getNumber());

        return pagination;
    }
hey chat i have this probleme is role i have it many so user can have more than one role the role validateur see everithing of demande all of them if status == nouvelle produit 
sponsor see just his demande if status == nouveau demande ou == nouvelle produit and if user have role sponsor and role validateur it connot see everthing if status  == nouvelle produit 
