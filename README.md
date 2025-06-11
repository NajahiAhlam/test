 @Transactional
    public ObjectPagination<DemandeRequestDTO> findAlldemandes(int page, int size, String sortDirection, String sortValue, Map<String, String> filters) throws PermissionException {
        User currentUser = userService.getAuthenticatedUser();

        Specification<DemandeQualification> spec = (root, query, criteriaBuilder) -> {
            List<Predicate> predicates = new ArrayList<>();

            if (userService.hasRole(currentUser, Role.Leader)) {
                predicates.add(criteriaBuilder.equal(root.get("reporter").get("user_id"), currentUser.getUser_id()));
            }
        

            List<String> validFilterKeys = Arrays.asList(
                    "objetDemande", "nomFiliale", "nom", "description", "porteurProjet", "porteurMetier",
                    "status", "type", "dateKickOff", "kickOffDecision",
                    "dateLancement", "dateModification", "dateCreation","typeDemande"
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

                    case "dateLancement":
                        String processedL = value.substring(4, value.indexOf("GMT")).trim();
                        DateTimeFormatter dfL = DateTimeFormatter.ofPattern("MMM dd yyyy HH:mm:ss", Locale.ENGLISH);
                        LocalDateTime dateL = LocalDateTime.parse(processedL, dfL);
                        LocalDateTime startOfDayL = dateL.toLocalDate().atStartOfDay();
                        LocalDateTime endOfDateL = dateL.plusDays(1).minusNanos(1);
                        predicates.add(criteriaBuilder.between(root.get("dateCreation"), startOfDayL, endOfDateL));
                        break;
                    case "dateKickOff":
                        String processed1K = value.substring(4, value.indexOf("GMT")).trim();
                        DateTimeFormatter df1K = DateTimeFormatter.ofPattern("MMM dd yyyy HH:mm:ss", Locale.ENGLISH);
                        LocalDateTime date1K = LocalDateTime.parse(processed1K, df1K);
                        LocalDateTime startOfDay1K = date1K.toLocalDate().atStartOfDay();
                        LocalDateTime endOfDate1K = date1K.plusDays(1).minusNanos(1);
                        predicates.add(criteriaBuilder.between(root.get("dateModification"), startOfDay1K, endOfDate1K));
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
hey chat i want if user is sponsorM in this demande to show it to him see just his demande and in demandeQualification
i have sponsorM type User and the other roles see alll demandes
