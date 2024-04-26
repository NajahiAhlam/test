onNewUtilisateur() {
    const buttonsConfig: NbWindowControlButtonsConfig = {
      minimize: false,
      maximize: false,
      fullScreen: true,
      close: true,
    };
    const nbWindowRef = this.windowService.open(DialogCrudProgrammesComponent, {
      title: ` Ajouter un programme`,
      buttons: buttonsConfig,
      context:{
        programme:this.programme
      }
    });

    this.initCloseListener(nbWindowRef); 
  }
  onEditUtilisateur(id: number | undefined) {
    if (id !== undefined) {
      this.gestionProgrammesService.getProgramme(id).subscribe(
        programme => {
          // Fetch role data for the user
          /*this.gestionProgrammesService.fetchRoles().subscribe(
            roles => {
              const buttonsConfig: NbWindowControlButtonsConfig = {
                minimize: false,
                maximize: false,
                fullScreen: true,
                close: true,
              };*/
              
              const nbWindowRef = this.windowService.open(DialogCrudProgrammesComponent, {
                title: `Modifier un programme`,
                context: { id: id,programme }, // Pass both user and role data as context
                //buttons: buttonsConfig,
              });
  
              this.initCloseListener(nbWindowRef); 
             // console.log("user", user);
              //console.log("role", roles);
            },
           
      );
    }
  }
