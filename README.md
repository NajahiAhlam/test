@Transactional
public ObjectPagination<DemandeRequestDTO> findAlldemandes(int page, int size, String sortDirection, String sortValue, Map<String, String> filters) throws PermissionException {
    User currentUser = userService.getAuthenticatedUser();

    Specification<DemandeQualification> spec = (root, query, criteriaBuilder) -> {
        List<Predicate> predicates = new ArrayList<>();

        if (userService.hasRole(currentUser, Role.Leader)) {
            // Leaders only see demandes they reported
            predicates.add(criteriaBuilder.equal(root.get("reporter").get("user_id"), currentUser.getUser_id()));
        } else if (userService.hasRole(currentUser, Role.SponsorM)) {
            // SponsorM only sees demandes where they are sponsorM
            predicates.add(criteriaBuilder.equal(root.get("sponsorM").get("user_id"), currentUser.getUser_id()));
        }
        // All other roles see all demandes

        List<String> validFilterKeys = Arrays.asList(
                "objetDemande", "nomFiliale", "nom", "description", "porteurProjet", "porteurMetier",
                "status", "type", "dateKickOff", "kickOffDecision",
                "dateLancement", "dateModification", "dateCreation", "typeDemande"
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
                    predicates.add(criteriaBuilder.between(
                            root.get("dateCreation"),
                            dateC.toLocalDate().atStartOfDay(),
                            dateC.toLocalDate().atTime(LocalTime.MAX)
                    ));
                    break;

                case "dateModification":
                    String processed1 = value.substring(4, value.indexOf("GMT")).trim();
                    DateTimeFormatter df1 = DateTimeFormatter.ofPattern("MMM dd yyyy HH:mm:ss", Locale.ENGLISH);
                    LocalDateTime date1 = LocalDateTime.parse(processed1, df1);
                    predicates.add(criteriaBuilder.between(
                            root.get("dateModif
