this.resolveMode();
    if (this.mode === 'UPDATE' && this.id) {
      this.gestionUtilisateurService.getUser(this.id).subscribe((user) => {
        this.initForm(user);
        this.roles$ = this.gestionUtilisateurService.fetchRoles().pipe(tap((_) => this.formGroup?.get('roles')?.setValue(user?.rolesIds)));
      });
    }
    if (this.mode === 'CREATE') {
      this.initForm();
      this.roles$ = this.gestionUtilisateurService.fetchRoles();
    }
    if (this.user) {
      this.initForm(this.user, this.roles); 
    }
