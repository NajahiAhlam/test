 for (ConditionDTO conditionDTO : dto.getConditions()) {
            // Fetch user by email (assigne)
            User assigneeUser = userRepository.findByEmail(conditionDTO.getAssigne());
            if (assigneeUser == null) {
                // Handle the case where the user is not found, maybe throw an exception or handle accordingly
                throw new RuntimeException("User with email " + conditionDTO.getAssigne() + " not found");
            }

            // Create a new Conditions object
            Conditions condition = new Conditions();
            condition.setAssigne(conditionDTO.getAssigne());
            condition.setCategorie(conditionDTO.getCategorie());
            condition.setCommentCond(conditionDTO.getCommentCond());
            condition.setDateEcheance(conditionDTO.getDateEcheance());
            condition.setDateLancement(conditionDTO.getDateLancement());
            condition.setDetail(conditionDTO.getDetail());
            condition.setEtat(conditionDTO.getEtat());
            condition.setNumberSemaineApresLancement(conditionDTO.getNumberSemaineApresLancement());

            // Set the User as the assignee
            condition.setAssigneeUser(assigneeUser);

            // Save the condition entity
            conditionsRepository.save(condition);
        }
