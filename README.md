changeProgramme(selected: string): void {
    this.selectedProgramme = selected;
    this.selectedProjets = '';
    this.filteredProjets = this.projets.filter(projet => projet.program === selected);
    this.filteredLeads = this.leads.filter(lead => lead.program === selected);
    this.selectedLead = ''; // This line is missing in your original code
}
