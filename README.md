    public List<Map<String, Object>> calculateStats(Long annee, String lead, String programme) {
        List<Projet> projets = projetRepository.findProjects(annee, lead, programme);

        Map<String, Map<String, Object>> leadStatsMap = new HashMap<>();

        for (Projet projet : projets) {
            Lead leadPrincipal = projet.getLeadPrincipal();
            List<Lead> leadContributeurs = projet.getLeadContributeurs();

            if (leadPrincipal != null) {
                String leadIntitule = leadPrincipal.getIntitule();
                leadStatsMap.putIfAbsent(leadIntitule, initializeLeadStatsMap());

                Map<String, Object> stats = leadStatsMap.get(leadIntitule);
                updateStats(stats, projet);
            }

            // Process leadContributeurs
            if (leadContributeurs != null) {
                for (Lead contributor : leadContributeurs) {
                    String contributorIntitule = contributor.getIntitule();

                    boolean isValidContributor = contributor.getBudgetRevises().stream().anyMatch(budget -> {
                        if ("SGABS".equals(contributorIntitule)) {
                            return budget.getExtern() != 0;
                        } else {
                            return budget.getIntern() != 0 && budget.getExtern() != 0;
                        }
                    });

                    if (isValidContributor) {
                        leadStatsMap.putIfAbsent(contributorIntitule, initializeLeadStatsMap());

                        Map<String, Object> stats = leadStatsMap.get(contributorIntitule);
                        updateStats(stats, projet);
                    }
                }
            }
        }

        // Prepare the result list
        List<Map<String, Object>> result = new ArrayList<>();
        for (Map.Entry<String, Map<String, Object>> entry : leadStatsMap.entrySet()) {
            Map<String, Object> leadStats = new HashMap<>(entry.getValue());
            leadStats.put("intitule", entry.getKey());
            result.add(leadStats);
        }

        return result;
    }


    private Map<String, Object> initializeLeadStatsMap() {
        Map<String, Object> stats = new HashMap<>();
        stats.put("projectsCount", 0);
        stats.put("logicielBudgete", 0.0);
        stats.put("materielBudgete", 0.0);
        stats.put("logicielEngage", 0.0);
        stats.put("materielEngage", 0.0);
        stats.put("logicielConsomme", 0.0);
        stats.put("materielConsomme", 0.0);
        stats.put("regieBudgete", 0.0);
        stats.put("regieEngage", 0.0);
        stats.put("regieConsomme", 0.0);
        stats.put("sgabsBudgete", 0.0);
        stats.put("sgabsEngage", 0.0);
        stats.put("sgabsConsomme", 0.0);
        stats.put("totalBudgete", 0.0);
        stats.put("totalEngage", 0.0);
        stats.put("totalConsomme", 0.0);
        stats.put("forfaitBudgete", 0.0);
        stats.put("forfaitEngage", 0.0);
        stats.put("forfaitConsomme", 0.0);
        return stats;
    }


    private void updateStats(Map<String, Object> stats, Projet projet) {
        BudgetCtbRevise budget = projet.getBudgetCtbRevise();
        if (budget != null) {
            List<P2p> p2ps = p2pRepository.findByCodeSciforma(projet.getRefSciforma());
            List<Sciforma> sciformas = sciformaRepository.findByProjetAndProgrammeAndAnnee(projet.getRefSciforma(), projet.getProgramme().getNom(), projet.getFdr().getAnnee().toString());


            stats.put("projectsCount", ((Number) stats.get("projectsCount")).longValue() + 1);

            double forfaitBudgete = budget.getForfait();
            // double logicielMaterielBudgete = budget.getLogiciel() + budget.getMateriel();
            double logicielBudgete = budget.getLogiciel();
            double materielBudgete = budget.getMateriel();
            double regieBudgete = budget.getRegie();
            double sgabsBudgete = budget.getSgabs();
            double ctbRevise = budget.getCtbHorsMasseSYcSgabs();

            // stats.put("logicielMaterielBudgete", (double) stats.get("logicielMaterielBudgete") + logicielMaterielBudgete);
            stats.put("logicielBudgete", (double) stats.get("logicielBudgete") + logicielBudgete);
            stats.put("materielBudgete", (double) stats.get("materielBudgete") + materielBudgete);
            stats.put("regieBudgete", (double) stats.get("regieBudgete") + regieBudgete);
            stats.put("sgabsBudgete", (double) stats.get("sgabsBudgete") + sgabsBudgete);
            stats.put("forfaitBudgete", (double) stats.get("forfaitBudgete") + forfaitBudgete);

            // stats.put("logicielMaterielEngage", (double) stats.get("logicielMaterielEngage") + calculateEngagePercentage(p2ps, "logiciel", "materiel", logicielMaterielBudgete));
            stats.put("logicielEngage", (double) stats.get("logicielEngage") + calculateEngagePercentage(p2ps, "LOGICIEL", ctbRevise));
            stats.put("materielEngage", (double) stats.get("materielEngage") + calculateEngagePercentage(p2ps, "MATERIEL", ctbRevise));
            stats.put("regieEngage", (double) stats.get("regieEngage") + calculateEngagePercentage(p2ps, "PRESTATIONS REGIE", ctbRevise));
            stats.put("sgabsEngage", (double) stats.get("sgabsEngage") + calculateEngagePercentage(p2ps, "SGABS", ctbRevise));
            stats.put("forfaitEngage", (double) stats.get("forfaitEngage") + calculateEngagePercentage(p2ps, "PRESTATIONS FORFAIT", ctbRevise));

            //stats.put("logicielMaterielConsomme", (double) stats.get("logicielMaterielConsomme") + calculateConsommePercentage(p2ps, "logiciel", logicielMaterielBudgete) +
            // calculateConsommePercentage(p2ps, "materiel", logicielMaterielBudgete));
            stats.put("logicielConsomme", (double) stats.get("logicielConsomme") + calculateConsommePercentage(p2ps, "LOGICIEL", ctbRevise));
            stats.put("materielConsomme", (double) stats.get("materielConsomme") + calculateConsommePercentage(p2ps, "MATERIEL", ctbRevise));
            stats.put("forfaitConsomme", (double) stats.get("forfaitConsomme") + calculateConsommePercentage(p2ps, "PRESTATIONS FORFAIT", ctbRevise));

            stats.put("regieConsomme", (double) stats.get("regieConsomme") + calculateSciformaConsomme(sciformas, regieBudgete));
            stats.put("sgabsConsomme", (double) stats.get("sgabsConsomme") + calculateConsommePercentage(p2ps, "sgabs", sgabsBudgete));

            stats.put("totalBudgete", (double) stats.get("totalBudgete") + forfaitBudgete  + logicielBudgete + materielBudgete +  regieBudgete + sgabsBudgete);
            stats.put("totalEngage", (double) stats.get("totalEngage") + calculateEngagePercentage(p2ps, "PRESTATIONS FORFAIT", forfaitBudgete) +
                    calculateEngagePercentage(p2ps, "LOGICIEL", logicielBudgete) +
                    calculateEngagePercentage(p2ps, "MATERIEL", materielBudgete) +
                    calculateEngagePercentage(p2ps, "PRESTATIONS REGIE", regieBudgete) +
                    calculateEngagePercentage(p2ps, "SGABS", sgabsBudgete));
            stats.put("totalConsomme", (double) stats.get("totalConsomme") + calculateConsommePercentage(p2ps, "forfait", forfaitBudgete) +
                    calculateConsommePercentage(p2ps, "LOGICIEL", logicielBudgete) +
                    calculateConsommePercentage(p2ps, "MATERIEL", materielBudgete) +
                    calculateSciformaConsomme(sciformas, regieBudgete) +
                    calculateConsommePercentage(p2ps, "SGABS", sgabsBudgete));
        }
    }



    private double calculateEngagePercentage(List<P2p> p2ps, String rubrique, double budget) {
        double totalEngagement = p2ps.stream()
                .filter(p -> p.getRubrique() != null && p.getRubrique().contains(rubrique))
                .mapToDouble(p -> {
                    try {
                        return Double.parseDouble(String.valueOf(p.getTotalEngegement()));
                    } catch (NumberFormatException e) {
                        return 0.0;
                    }
                })
                .sum();
        return budget != 0 ? (totalEngagement / budget) * 100 : 0.0;
    }


   /* private double calculateEngagePercentage(List<P2p> p2ps, String rubrique1, String rubrique2, double budget) {
        double totalEngagement = p2ps.stream()
                .filter(p -> p.getRubrique() != null && (p.getRubrique().contains(rubrique1) || p.getRubrique().contains(rubrique2)))
                .mapToDouble(p -> {
                    try {
                        return Double.parseDouble(String.valueOf(p.getTotalEngegement()));
                    } catch (NumberFormatException e) {
                        return 0.0;
                    }
                })
                .sum();
        return budget != 0 ? (totalEngagement / budget) * 100 : 0.0;
    }*/

    private double calculateConsommePercentage(List<P2p> p2ps, String rubrique, double budget) {
        double totalConsomme = p2ps.stream()
                .filter(p -> p.getRubrique() != null && p.getRubrique().contains(rubrique))
                .mapToDouble(p -> {
                    try {
                        return Double.parseDouble(String.valueOf(p.getTotalEngegement()));
                    } catch (NumberFormatException e) {
                        return 0.0;
                    }
                })
                .sum();
        return budget != 0 ? (totalConsomme / budget) * 100 : 0.0;
    }




    private double calculateSciformaConsomme(List<Sciforma> sciformas, double budget) {
        double totalConsomme = sciformas.stream()
                .mapToDouble(s -> {
                    try {
                        return s.getCashOutExterneConsomme();
                    } catch (NumberFormatException e) {
                        return 0.0;
                    }
                })
                .sum();
        return budget != 0 ? (totalConsomme / budget) * 100 : 0.0;
    }
