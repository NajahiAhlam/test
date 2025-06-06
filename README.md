export class StartRequestFormComponent {
  form!: FormGroup;
  question!: Observable<Question[]>;
  filteredPoreturProjetOptions$!: Observable<any[]>;
  filteredPorteurMetierOptions$!: Observable<any[]>;
  filteredSponsorOptions$!: Observable<any[]>;
  sponsor: any[] = [];
  porteurProjet: any[] = [];
  porteurMetier: any[] = [];
  users: User[] = [];
  questionList: any[] = [];
  filiales = ['FILIALE1', 'FILIALE2', 'FILIALE3'];
  showAllQuestions = false;
  expandedParents: { [key: number]: boolean } = {};
  submitted: boolean = false

  checkedQuestions: Set<string> = new Set(); // Track checked question numbers
  showAdditionalContent: boolean = false; // Control additional content visibility
  closeAlert: boolean= true;
  message: string='';

  constructor(
    private formBuilder: FormBuilder,
    private _wkfService: WkfService,
    private router: Router,
    private windowRef: NbDialogRef<StartRequestFormComponent>,
    private userService: UserService,
    private cdr: ChangeDetectorRef,
    private utilsService: UtilsService,

  ) {}

  ngOnInit() {
    this.initFormGroup();
   /* this.question = this._wkfService.getAll();
    this.question.subscribe(data => {
      console.log(data);
      // Do something with the data here
    });*/
    this._wkfService.getAll().subscribe((data: any[]) => {
      this.questionList = data.map(q => ({ ...q, response: false }));
    });
   console.log("question: ",this.question)


   this.userService.getUsers("SponsorM").subscribe(sponsors => {
    this.sponsor = sponsors.filter(el=>el.enabled===true);
    this.filteredSponsorOptions$ = this.form.get('sponsor')!.valueChanges.pipe(
      startWith(''),
      map(filterString => this.filterOptions(filterString, this.sponsor, 'email'))
    );
  });

  // Get Lead
  this.userService.getUsers("PorteurP").subscribe(leads => {
    this.porteurProjet = leads.filter(el=>el.enabled===true);
    this.filteredPoreturProjetOptions$ = this.form.get('porteurProjet')!.valueChanges.pipe(
      startWith(''),
      map(filterString => this.filterOptions(filterString, this.porteurProjet, 'email'))
    );
  });

  // Get Corndinateur
  this.userService.getUsers("PorteurM").subscribe(cordianteurs => {
    this.porteurMetier = cordianteurs.filter(el=>el.enabled===true);
    this.filteredPorteurMetierOptions$ = this.form.get('porteurMetier')!.valueChanges.pipe(
      startWith(''),
      map(filterString => this.filterOptions(filterString, this.porteurMetier, 'email'))
    );
  });

  }

  filterOptions(filterString: string, options: any[], field: string): any[] {
  if (!filterString || typeof filterString !== 'string') {
    return options;
  }
  const filterValue = filterString.toLowerCase();
  return options.filter(option => option[field].toLowerCase().includes(filterValue));
}

    // Check if question has sub-questions
    hasSubQuestions(num: string): boolean {
      return this.questionList.some(q => q.numQuestion.startsWith(num + "."));
    }

    // Get sub-questions for a parent question
    getSubQuestionsold(parentNum: any): any[] {
      return this.questionList.filter(q => q.numQuestion.startsWith(parentNum + "."));
    }
    getSubQuestions(parentNum: string): any[] {
      return this.questionList
        .filter(q => q.numQuestion.startsWith(parentNum + "."))
        .sort((a, b) => parseFloat(a.numQuestion) - parseFloat(b.numQuestion)); // Sort numerically
    }



    toggleSubQuestions(parentNum: any) {
      this.expandedParents[parentNum] = !this.expandedParents[parentNum];
    }
    getFilteredParentQuestionsold(): any[] {
      if(!this.showAdditionalContent){
              return this.questionList.filter(q => !q.numQuestion.includes('.') && parseInt(q.numQuestion) >= 1 && parseInt(q.numQuestion) <= 5);
      }
     return this.questionList.filter(q => !q.numQuestion.includes('.') && parseInt(q.numQuestion) >= 1 && parseInt(q.numQuestion) > 5);;
    }

    getFilteredParentQuestions(): any[] {
      return this.showAdditionalContent
        ? this.questionList.filter(q => !q.numQuestion.includes('.') && parseInt(q.numQuestion) >= 1)
        : this.questionList.filter(q => !q.numQuestion.includes('.') && parseInt(q.numQuestion) >= 1 && parseInt(q.numQuestion) <= 5);
    }




  toggleResponseold(question: any): void {
    question.response = !question.response;

    if (question.response) {
      this.checkedQuestions.add(question.numQuestion);
    } else {
      this.checkedQuestions.delete(question.numQuestion);
    }

    this.checkPopupCondition();
    setTimeout(() => this.cdr.detectChanges(), 0);
  }
  checkPopupConditionold(): void {
    if (this.checkedQuestions.has("1") && this.checkedQuestions.size > 1) {
     // alert("You have selected question 1 and another question!"); // Show popup
      this.showAdditionalContent = true; // Display additional content

    //  this.utilsService.displaySucess("Il s'agit d'un Nouveau Produit au sens de l'instruction SG. Un CNP est requis pour valider le lancement de ce produit. Merci de continuer le questionnaire.","Eligible")
 
    }
  }

checkPopupCondition(): void {
  const hasQ1 = Array.from(this.checkedQuestions).some(q => q === "1" || q.startsWith("1."));
  const hasAnother = Array.from(this.checkedQuestions).some(q => {
    if (q === "1" || q.startsWith("1.")) return false;
    const num = parseInt(q.split('.')[0]);
    return num >= 1 && num <= 5;
  });

  const newShow = hasQ1 && hasAnother;

  if (newShow && !this.showAdditionalContent) {
    // condition just became true → show alert again
    this.closeAlert = true; // reopen the alert
  }

  this.showAdditionalContent = newShow;

}
toggleResponse(question: any): void {
  question.response = !question.response;

  if (question.response) {
    this.checkedQuestions.add(question.numQuestion);
  } else {
    this.checkedQuestions.delete(question.numQuestion);
  }

  this.checkPopupCondition();
  setTimeout(() => this.cdr.detectChanges(), 0);
}

  getExtraQuestions(): any[] {
    return this.questionList.filter(q => parseInt(q.numQuestion) > 5);
  }


  initFormGroup() {
    this.form = this.formBuilder.group({
      filiale: ['', Validators.required],
      type: [''],
      date: [''],
      dateReel: [''],
      porteurProjet: ['', Validators.required],
      porteurMetier: ['', Validators.required],
      sponsor: ['', Validators.required],
      objet: ['', Validators.required],
      description: ['']
    });
  }


  resetForm() {
    this.form.reset();
    this.form.get('porteurProjet')!.setValue(''); 
    this.questionList = this.questionList.map(question => ({
      ...question,
      response: false
    }));

    this.cdr.detectChanges();
  }
  getEligibilityQuestions(): any[] {
  return this.questionList.filter(q => 
    !q.numQuestion.includes('.') &&
    parseInt(q.numQuestion) >= 1 && parseInt(q.numQuestion) <= 5
  );
}

getCNPQuestions(): any[] {
  return this.showAdditionalContent
    ? this.questionList.filter(q => 
        !q.numQuestion.includes('.') &&
        parseInt(q.numQuestion) >= 6 && parseInt(q.numQuestion) <= 15
      )
    : [];
}


  submitt(): void {
    if (this.form.valid) {
      const mappedQuestions = this.questionList.map(q => {
        const numParts = q.numQuestion.split('.');
        const parentQuestionId = numParts.length > 1 ? Number(numParts[0]) : Number(q.numQuestion);
        const subQuestionId = numParts.length > 1 ? q.numQuestion : null;

        return {
          id: q.id,
          parentQuestionId,
          subQuestionId,
          answered: q.response
        };
      });

      const demande: DemandeQualification = {
        ...this.form.value,
        demandeQualificationQuestionReponses: mappedQuestions
      };

      console.log("Final request payload:", demande);

      this._wkfService.saveDemande(demande).subscribe(
        (response) => {
          console.log('Demande saved successfully:', response);
          this.closeWindow();
        },
        (error) => {
          console.error("Error saving demande:", error);
        }
      );
    } else {
      console.warn('Form is invalid, please check the required fields.');
    }
  }
  getCriticiteLevel(): string {
    const filteredQuestions = this.questionList.filter(q => {
      const num = parseInt(q.numQuestion);
      return num >= 6 && num <= 15;
    });

     const num1Unchecked = this.questionList.some(q => q.numQuestion === "1" && q.response === false);
    if (num1Unchecked) {
      return "Non_ELIGIBLE";
    }

    const allFalse = filteredQuestions.every(q => q.response === false);

    const onlyTenElevenTrue = filteredQuestions.every(q =>
      (q.numQuestion === "10" || q.numQuestion === "11") ? q.response === true : q.response === false
    );

    if (allFalse) {
      return "ELEVEE";
    } else if (onlyTenElevenTrue) {
      return "MOYENNE";
    }

    return "ELEVEE";
  }


  submit(): void {
    const subTitleSucces = "Succès";
    const titleSucces = "Demande ajouté avec succès";

    if (this.form.invalid) {
      this.utilsService.displayWarning("Formulaire invalide, merci de renseigner les champs obligatoire","Erreur de saisie")
      return;
    }

    if (this.form.valid) {
      const criticite = this.getCriticiteLevel();
        const mappedQuestions = this.questionList.map(q => {
            const numParts = q.numQuestion.split('.');

            let parentQuestionId: number | null = null;
            let subQuestionId: number | null = null;

            if (numParts.length === 1) {
                parentQuestionId = q.id;
            } else {
                parentQuestionId = this.questionList.find(item => item.numQuestion === numParts[0])?.id || null;
                subQuestionId = q.id;
            }

            return {
                estTitre: q.estTitre ?? false,
                answered: q.response,
                parentQuestionId,
                subQuestionId
            };
        });

        const demande: DemandeQualification = {
            ...this.form.value,
            demandeQualificationQuestionReponses: mappedQuestions,
            criticite: criticite,
        };
        console.log("criticite est : ",criticite)

       // console.log("Final request payload:", JSON.stringify(demande, null, 2));

        this._wkfService.saveDemande(demande).subscribe(
            (response) => {
              this.utilsService.displaySucess(subTitleSucces, titleSucces);
                console.log('Demande saved successfully:', response);

                this.closeWindow();
                  this.router.navigate(['pages/demande-details',response.id]);
                this.utilsService.displaySucess(subTitleSucces, titleSucces);
            },
            (error) => {
                console.error('Error saving demande:', error);
            }
        );
    } else {
        console.warn('Form is invalid, please check the required fields.');
    }
}

  closeWindow(): void {
    this.windowRef.close();
  }
  closeAlerte() {
    this.closeAlert = false
  }

calculQuestionnaireCriticite() {
  // Is question 1 unchecked?
  const num1 = this.questionList.find(q => q.numQuestion === "1");
  const num1Checked = num1 && num1.response === true;

  // Are all questions except 1 unchecked?
  const onlyNum1Checked = num1Checked &&
    this.questionList
      .filter(q => q.numQuestion !== "1")
      .every(q => q.response === false);

  if (!num1Checked || onlyNum1Checked) {
    this.message = "Le questionnaire n'est pas éligible.";
    return;
  }

  // Continue with your criticité logic:
  const filteredQuestions = this.questionList.filter(q => {
    const num = parseInt(q.numQuestion);
    return num >= 6 && num <= 15;
  });

  const allFalse = filteredQuestions.every(q => q.response === false);

  const onlyTenElevenTrue = filteredQuestions.every(q =>
    (q.numQuestion === "10" || q.numQuestion === "11") ? q.response === true : q.response === false
  );

  if (allFalse) {
    this.message = "Le niveau de criticité est ÉLEVÉE.";
  } else if (onlyTenElevenTrue) {
    this.message = "Le niveau de criticité est MOYENNE.";
  } else {
    this.message = "Le niveau de criticité est ÉLEVÉE.";
  }
}


}export class StartRequestFormComponent {
  form!: FormGroup;
  question!: Observable<Question[]>;
  filteredPoreturProjetOptions$!: Observable<any[]>;
  filteredPorteurMetierOptions$!: Observable<any[]>;
  filteredSponsorOptions$!: Observable<any[]>;
  sponsor: any[] = [];
  porteurProjet: any[] = [];
  porteurMetier: any[] = [];
  users: User[] = [];
  questionList: any[] = [];
  filiales = ['FILIALE1', 'FILIALE2', 'FILIALE3'];
  showAllQuestions = false;
  expandedParents: { [key: number]: boolean } = {};
  submitted: boolean = false

  checkedQuestions: Set<string> = new Set(); // Track checked question numbers
  showAdditionalContent: boolean = false; // Control additional content visibility
  closeAlert: boolean= true;
  message: string='';

  constructor(
    private formBuilder: FormBuilder,
    private _wkfService: WkfService,
    private router: Router,
    private windowRef: NbDialogRef<StartRequestFormComponent>,
    private userService: UserService,
    private cdr: ChangeDetectorRef,
    private utilsService: UtilsService,

  ) {}

  ngOnInit() {
    this.initFormGroup();
   /* this.question = this._wkfService.getAll();
    this.question.subscribe(data => {
      console.log(data);
      // Do something with the data here
    });*/
    this._wkfService.getAll().subscribe((data: any[]) => {
      this.questionList = data.map(q => ({ ...q, response: false }));
    });
   console.log("question: ",this.question)


   this.userService.getUsers("SponsorM").subscribe(sponsors => {
    this.sponsor = sponsors.filter(el=>el.enabled===true);
    this.filteredSponsorOptions$ = this.form.get('sponsor')!.valueChanges.pipe(
      startWith(''),
      map(filterString => this.filterOptions(filterString, this.sponsor, 'email'))
    );
  });

  // Get Lead
  this.userService.getUsers("PorteurP").subscribe(leads => {
    this.porteurProjet = leads.filter(el=>el.enabled===true);
    this.filteredPoreturProjetOptions$ = this.form.get('porteurProjet')!.valueChanges.pipe(
      startWith(''),
      map(filterString => this.filterOptions(filterString, this.porteurProjet, 'email'))
    );
  });

  // Get Corndinateur
  this.userService.getUsers("PorteurM").subscribe(cordianteurs => {
    this.porteurMetier = cordianteurs.filter(el=>el.enabled===true);
    this.filteredPorteurMetierOptions$ = this.form.get('porteurMetier')!.valueChanges.pipe(
      startWith(''),
      map(filterString => this.filterOptions(filterString, this.porteurMetier, 'email'))
    );
  });

  }

  filterOptions(filterString: string, options: any[], field: string): any[] {
  if (!filterString || typeof filterString !== 'string') {
    return options;
  }
  const filterValue = filterString.toLowerCase();
  return options.filter(option => option[field].toLowerCase().includes(filterValue));
}

    // Check if question has sub-questions
    hasSubQuestions(num: string): boolean {
      return this.questionList.some(q => q.numQuestion.startsWith(num + "."));
    }

    // Get sub-questions for a parent question
    getSubQuestionsold(parentNum: any): any[] {
      return this.questionList.filter(q => q.numQuestion.startsWith(parentNum + "."));
    }
    getSubQuestions(parentNum: string): any[] {
      return this.questionList
        .filter(q => q.numQuestion.startsWith(parentNum + "."))
        .sort((a, b) => parseFloat(a.numQuestion) - parseFloat(b.numQuestion)); // Sort numerically
    }



    toggleSubQuestions(parentNum: any) {
      this.expandedParents[parentNum] = !this.expandedParents[parentNum];
    }
    getFilteredParentQuestionsold(): any[] {
      if(!this.showAdditionalContent){
              return this.questionList.filter(q => !q.numQuestion.includes('.') && parseInt(q.numQuestion) >= 1 && parseInt(q.numQuestion) <= 5);
      }
     return this.questionList.filter(q => !q.numQuestion.includes('.') && parseInt(q.numQuestion) >= 1 && parseInt(q.numQuestion) > 5);;
    }

    getFilteredParentQuestions(): any[] {
      return this.showAdditionalContent
        ? this.questionList.filter(q => !q.numQuestion.includes('.') && parseInt(q.numQuestion) >= 1)
        : this.questionList.filter(q => !q.numQuestion.includes('.') && parseInt(q.numQuestion) >= 1 && parseInt(q.numQuestion) <= 5);
    }




  toggleResponseold(question: any): void {
    question.response = !question.response;

    if (question.response) {
      this.checkedQuestions.add(question.numQuestion);
    } else {
      this.checkedQuestions.delete(question.numQuestion);
    }

    this.checkPopupCondition();
    setTimeout(() => this.cdr.detectChanges(), 0);
  }
  checkPopupConditionold(): void {
    if (this.checkedQuestions.has("1") && this.checkedQuestions.size > 1) {
     // alert("You have selected question 1 and another question!"); // Show popup
      this.showAdditionalContent = true; // Display additional content

    //  this.utilsService.displaySucess("Il s'agit d'un Nouveau Produit au sens de l'instruction SG. Un CNP est requis pour valider le lancement de ce produit. Merci de continuer le questionnaire.","Eligible")
 
    }
  }

checkPopupCondition(): void {
  const hasQ1 = Array.from(this.checkedQuestions).some(q => q === "1" || q.startsWith("1."));
  const hasAnother = Array.from(this.checkedQuestions).some(q => {
    if (q === "1" || q.startsWith("1.")) return false;
    const num = parseInt(q.split('.')[0]);
    return num >= 1 && num <= 5;
  });

  const newShow = hasQ1 && hasAnother;

  if (newShow && !this.showAdditionalContent) {
    // condition just became true → show alert again
    this.closeAlert = true; // reopen the alert
  }

  this.showAdditionalContent = newShow;

}
toggleResponse(question: any): void {
  question.response = !question.response;

  if (question.response) {
    this.checkedQuestions.add(question.numQuestion);
  } else {
    this.checkedQuestions.delete(question.numQuestion);
  }

  this.checkPopupCondition();
  setTimeout(() => this.cdr.detectChanges(), 0);
}

  getExtraQuestions(): any[] {
    return this.questionList.filter(q => parseInt(q.numQuestion) > 5);
  }


  initFormGroup() {
    this.form = this.formBuilder.group({
      filiale: ['', Validators.required],
      type: [''],
      date: [''],
      dateReel: [''],
      porteurProjet: ['', Validators.required],
      porteurMetier: ['', Validators.required],
      sponsor: ['', Validators.required],
      objet: ['', Validators.required],
      description: ['']
    });
  }


  resetForm() {
    this.form.reset();
    this.form.get('porteurProjet')!.setValue(''); 
    this.questionList = this.questionList.map(question => ({
      ...question,
      response: false
    }));

    this.cdr.detectChanges();
  }
  getEligibilityQuestions(): any[] {
  return this.questionList.filter(q => 
    !q.numQuestion.includes('.') &&
    parseInt(q.numQuestion) >= 1 && parseInt(q.numQuestion) <= 5
  );
}

getCNPQuestions(): any[] {
  return this.showAdditionalContent
    ? this.questionList.filter(q => 
        !q.numQuestion.includes('.') &&
        parseInt(q.numQuestion) >= 6 && parseInt(q.numQuestion) <= 15
      )
    : [];
}


  submitt(): void {
    if (this.form.valid) {
      const mappedQuestions = this.questionList.map(q => {
        const numParts = q.numQuestion.split('.');
        const parentQuestionId = numParts.length > 1 ? Number(numParts[0]) : Number(q.numQuestion);
        const subQuestionId = numParts.length > 1 ? q.numQuestion : null;

        return {
          id: q.id,
          parentQuestionId,
          subQuestionId,
          answered: q.response
        };
      });

      const demande: DemandeQualification = {
        ...this.form.value,
        demandeQualificationQuestionReponses: mappedQuestions
      };

      console.log("Final request payload:", demande);

      this._wkfService.saveDemande(demande).subscribe(
        (response) => {
          console.log('Demande saved successfully:', response);
          this.closeWindow();
        },
        (error) => {
          console.error("Error saving demande:", error);
        }
      );
    } else {
      console.warn('Form is invalid, please check the required fields.');
    }
  }
  getCriticiteLevel(): string {
    const filteredQuestions = this.questionList.filter(q => {
      const num = parseInt(q.numQuestion);
      return num >= 6 && num <= 15;
    });

     const num1Unchecked = this.questionList.some(q => q.numQuestion === "1" && q.response === false);
    if (num1Unchecked) {
      return "Non_ELIGIBLE";
    }

    const allFalse = filteredQuestions.every(q => q.response === false);

    const onlyTenElevenTrue = filteredQuestions.every(q =>
      (q.numQuestion === "10" || q.numQuestion === "11") ? q.response === true : q.response === false
    );

    if (allFalse) {
      return "ELEVEE";
    } else if (onlyTenElevenTrue) {
      return "MOYENNE";
    }

    return "ELEVEE";
  }


  submit(): void {
    const subTitleSucces = "Succès";
    const titleSucces = "Demande ajouté avec succès";

    if (this.form.invalid) {
      this.utilsService.displayWarning("Formulaire invalide, merci de renseigner les champs obligatoire","Erreur de saisie")
      return;
    }

    if (this.form.valid) {
      const criticite = this.getCriticiteLevel();
        const mappedQuestions = this.questionList.map(q => {
            const numParts = q.numQuestion.split('.');

            let parentQuestionId: number | null = null;
            let subQuestionId: number | null = null;

            if (numParts.length === 1) {
                parentQuestionId = q.id;
            } else {
                parentQuestionId = this.questionList.find(item => item.numQuestion === numParts[0])?.id || null;
                subQuestionId = q.id;
            }

            return {
                estTitre: q.estTitre ?? false,
                answered: q.response,
                parentQuestionId,
                subQuestionId
            };
        });

        const demande: DemandeQualification = {
            ...this.form.value,
            demandeQualificationQuestionReponses: mappedQuestions,
            criticite: criticite,
        };
        console.log("criticite est : ",criticite)

       // console.log("Final request payload:", JSON.stringify(demande, null, 2));

        this._wkfService.saveDemande(demande).subscribe(
            (response) => {
              this.utilsService.displaySucess(subTitleSucces, titleSucces);
                console.log('Demande saved successfully:', response);

                this.closeWindow();
                  this.router.navigate(['pages/demande-details',response.id]);
                this.utilsService.displaySucess(subTitleSucces, titleSucces);
            },
            (error) => {
                console.error('Error saving demande:', error);
            }
        );
    } else {
        console.warn('Form is invalid, please check the required fields.');
    }
}

  closeWindow(): void {
    this.windowRef.close();
  }
  closeAlerte() {
    this.closeAlert = false
  }

calculQuestionnaireCriticite() {
  // Is question 1 unchecked?
  const num1 = this.questionList.find(q => q.numQuestion === "1");
  const num1Checked = num1 && num1.response === true;

  // Are all questions except 1 unchecked?
  const onlyNum1Checked = num1Checked &&
    this.questionList
      .filter(q => q.numQuestion !== "1")
      .every(q => q.response === false);

  if (!num1Checked || onlyNum1Checked) {
    this.message = "Le questionnaire n'est pas éligible.";
    return;
  }

  // Continue with your criticité logic:
  const filteredQuestions = this.questionList.filter(q => {
    const num = parseInt(q.numQuestion);
    return num >= 6 && num <= 15;
  });

  const allFalse = filteredQuestions.every(q => q.response === false);

  const onlyTenElevenTrue = filteredQuestions.every(q =>
    (q.numQuestion === "10" || q.numQuestion === "11") ? q.response === true : q.response === false
  );

  if (allFalse) {
    this.message = "Le niveau de criticité est ÉLEVÉE.";
  } else if (onlyTenElevenTrue) {
    this.message = "Le niveau de criticité est MOYENNE.";
  } else {
    this.message = "Le niveau de criticité est ÉLEVÉE.";
  }
}


}
