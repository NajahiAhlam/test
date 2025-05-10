 .map(c -> {
                        // Resolve User by email from the repository passed in
                        User user = userRepository.findByEmail(c.getAssigne())
                                .orElseThrow(() -> new RuntimeException("User not found: " + c.getAssigne()));

                        return Conditions.builder()
                                .assigne(user)
                                .categorie(c.getCategorie())
                                .commentCond(c.getCommentCond())
                                .dateEcheance(c.getDateEcheance())
                                .dateLancement(c.getDateLancement())
                                .detail(c.getDetail())
                                .etat(c.getEtat())
                                .numberSemaineApresLancement(c.getNumberSemaineApresLancement())
                                .build();
                    })
                    .collect(Collectors.toList());Non-static field 'userRepository' cannot be referenced from a static context
