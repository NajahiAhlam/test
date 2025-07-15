const routes: Routes = [
  {
    title: "MYCNP - SAHAM Bank",
    path: '',
    component: PagesComponent,
    children: [
{
    path: '',
    component:ListDemandeComponent
},
      {
      
        path: 'details/:id',
        component: DemandeDetailsComponent,
         canActivate: [RoleGuard],
         
        data: {roles:['CoordinateurCNP', "Leader", "SponsorM","Validateur"]}
        
      },
            {
        path: 'utilisateurs-list',
        component: UsersListComponent,
         canActivate: [RoleGuard],
         
        data: {roles:['CoordinateurCNP']}
        
      },


      {
        path: 'risque-details/:id',
        component: RisqueConditionDetailsComponent,
        canActivate: [RoleGuard],

        data: {
          roles: ['CoordinateurCNP', "Leader", "SponsorM","Validateur"]
        }
      },

      {
      //  title: "GestionQuestions",
        path: 'Gestion-Questions',
        component: GestionQuestionsComponent,
        canActivate: [RoleGuard],
        data: {
          roles: ['CoordinateurCNP']
        }
      },


      {
      //  title: "add-question",
        path: 'add-question',
        component: AddQuestionComponent,
        canActivate: [RoleGuard],
        data: {
          roles: ['CoordinateurCNP']
        }
      },
      {
        path: 'Statut-Produit',
        component: StatutProduitListComponent,
        canActivate: [RoleGuard],

        data: {
          roles: ['CoordinateurCNP']
        }
      },

      {
       // title: "CategoryQuestions",
        path: 'Category-Questions',
        component: CategoryQuestionsComponent,
        canActivate: [RoleGuard],
        data: {
          roles: ['CoordinateurCNP']
        }
      },
      {
        // title: "CategoryQuestions",
         path: 'validateur-interface',
         component: ValidateurInterComponent,
         canActivate: [RoleGuard],
         data: {
           roles: ['CoordinateurCNP']
         }
       },

      
      {
      //  title: "dashBordLeader",
        path: 'dashBord-leader',
        component: DashboardLeaderComponent,
        canActivate: [RoleGuard],

        data: {
          roles: ['Leader']
        }
      },
      {
        path: "suivit-condition",
        component:SuivitConditionComponent,
        canActivate: [RoleGuard],
        data: {
          roles: ['Leader','SponsorM','CoordinateurCNP']
        }
      },
 
    

  
      {
        path: 'referentiel/StatutProduit',
        component: StatutProduitListComponent,
        canActivate: [RoleGuard],

        data: {
          roles: ['CoordinateurCNP']
        }
      },
     

      {
        path: 'referentiel/typeInstance',
        component: StatutProduitListComponent,
        canActivate: [RoleGuard],

        data: {
          roles: ['CoordinateurCNP']
        }
      },
      {
        path: 'referentiel/risques',
        component: RisqueListComponent,
        canActivate: [RoleGuard],

        data: {
          roles: ['CoordinateurCNP']
        }
      },
      {
        //  title: "creation du nouveau produit",
          path: 'demande',
          component: ListDemandeComponent,
          canActivate: [RoleGuard],

          data: {
            roles: ['CoordinateurCNP','Leader','SponsorM',"Validateur"]
          }
      },
        {
          path: 'demande-Non-Eligible',
          component: DemandeListNonEligibleComponent,
          canActivate: [RoleGuard],

          data: {
            roles: ['CoordinateurCNP','Leader','SponsorM',"Validateur"]
          }
      },
        {
        //  title: "creation du nouveau produit",
          path: 'produit',
          component: ProduitListComponent,
          canActivate: [RoleGuard],

          data: {
            roles: ['CoordinateurCNP','Leader','SponsorM',"Validateur"]
          }
      },
      {
        //  title: "creation du nouveau produit",
          path: 'reporting',
          component: PilotageConditionsComponent,
          canActivate: [RoleGuard],

          data: {
            roles: ['CoordinateurCNP']
          }
      },
    
      {
      //  title: "demande_qualification_edit",
        path: 'nouveau-produit',  // For editing an existing demande
        component: NouveauProduitComponent,
     //   canActivate: [RoleGuard],

        data: {
        //  roles: ['Leader']
        }
      },
     
      {
       // title: "user  GEMS SG",
        path: 'user-form',
        component: UsersComponent,
        canActivate: [RoleGuard],

        data: {
          roles: ['GestionnaireH']
        }
      },
      {
        path: '',
        redirectTo: '',
        pathMatch: 'full',
      },
      {
        path: '**',
        component: NotFoundComponent,
      },

    ]
  },



]

@NgModule({
  imports: [RouterModule.forChild(routes)],
  exports: [RouterModule],
})
export class PagesRoutingModule { }
