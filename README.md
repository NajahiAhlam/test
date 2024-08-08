public ObjectPagination<ProjetDTO> findAllInitiatives(int page, int size, String sortDirection, String sortValue, Map<String, String> filters) throws SsoException {
    // ... existing code ...

    ObjectPagination<ProjetDTO> pagination = new ObjectPagination<>();
    pagination.setContent(pageOfInitiative.stream()
            .map(initiative -> {
                ProjetDTO dto = modelMapper.map(initiative, ProjetDTO.class);
                // Ensure categoriIds are set
                dto.setCategoriIds(initiative.getCategories().stream().map(Categorie::getId).collect(Collectors.toList()));
                return dto;
            })
            .collect(Collectors.toList()));

    // ... existing code ...
    return pagination;
}
@ManyToMany(fetch = FetchType.LAZY)
@JoinTable(name = "projet_categories",
        joinColumns = @JoinColumn(name = "projet_id"),
        inverseJoinColumns = @JoinColumn(name = "categorie_id"))
private List<Categorie> categories;
modelMapper.addMappings(new PropertyMap<Projet, ProjetDTO>() {
    @Override
    protected void configure() {
        map().setCategoriIds(source.getCategories().stream().map(Categorie::getId).collect(Collectors.toList()));
    }
});
private Projet convertDTOToProjet(ProjetDTO dto) {
    Projet projet = modelMapper.map(dto, Projet.class);
    System.out.println("Categories IDs: " + dto.getCategoriesIds()); // Debugging line
    setCategoriess(projet, dto.getCategoriesIds());
    updateProjetFields(projet);
    cloneBudgets(projet);
    logConversionHistory(projet);
    return projet;
}
private void setCategoriess(Projet projet, List<Long> categoryIds) {
    if (categoryIds != null && !categoryIds.isEmpty()) {
        List<Categorie> categories = categorieRepository.findAllById(categoryIds);
        if (categories.isEmpty()) {
            // Log a warning or handle the case where no categories are found
            System.out.println("No categories found for IDs: " + categoryIds);
        }
        projet.setCategories(categories);
    } else {
        projet.setCategories(Collections.emptyList());
    }
}

