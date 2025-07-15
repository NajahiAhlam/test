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
        path: 'Statut-Produit',
        component: StatutProduitListComponent,
        canActivate: [RoleGuard],

        data: {
          roles: ['CoordinateurCNP']
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
        path: 'referentiel/risques',
        component: RisqueListComponent,
        canActivate: [RoleGuard],

        data: {
          roles: ['CoordinateurCNP']
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

const routes: Routes = [
  {
   path: 'pages',
   loadChildren: () => import('./pages/pages.module')
     .then(m => m.PagesModule),
     resolve: {
      initialData: AppResolver
    },


  },
  { path: '', redirectTo: 'pages/demande', pathMatch: 'full' },

  { path: '**', redirectTo: 'pages/not-found' },
];

@NgModule({
  imports: [RouterModule.forRoot(routes, {onSameUrlNavigation: 'reload'})],
  exports: [RouterModule],
  providers: [AppResolver]
})
export class AppRoutingModule { }
@Injectable({
  providedIn: 'root'
})
export class Role {

  roleLeader() {
    const roles = JSON.parse(sessionStorage.getItem('roles') || '[]');
    console.log(roles.includes("Leader") || roles.includes("CoordinateurCNP"));

    return roles.includes("Leader") || roles.includes("CoordinateurCNP");
  }

  roleSponsore() {
    const roles = JSON.parse(sessionStorage.getItem('roles') || '[]');
    console.log(roles.includes("SponsorM"));

    return roles.includes("SponsorM");
  }

  roleHabilitation() {
    const roles = JSON.parse(sessionStorage.getItem('roles') || '[]');
    console.log(roles.includes("GestionnaireH"));

    return roles.includes("GestionnaireH");
  }

  getRoles(): string[] {
    const roles = sessionStorage.getItem('roles');
    return roles ? JSON.parse(roles) : [];
  }

  hasRole(role: string): boolean {
    const roles = this.getRoles();
    return roles.includes(role);
  }

  roleLeaderUnic() {
    const roles = JSON.parse(sessionStorage.getItem('roles') || '[]');
    return roles.includes("Leader");
  }

  roleCordinateurUnic() {
    const roles = JSON.parse(sessionStorage.getItem('roles') || '[]');
    console.log(roles.includes("CoordinateurCNP"));
    return roles.includes("CoordinateurCNP");
  }

  roleCordinateurSponsor() {
    const roles = JSON.parse(sessionStorage.getItem('roles') || '[]');
    console.log(roles.includes("CoordinateurCNP"));
    return roles.includes("SponsorM") || roles.includes("CoordinateurCNP");
  }
  roleCordinateurSponsorLeade() {
    const roles = JSON.parse(sessionStorage.getItem('roles') || '[]');
    console.log(roles.includes("CoordinateurCNP"));
    return roles.includes("SponsorM") || roles.includes("CoordinateurCNP")|| roles.includes("Leader");
  }
}
@Injectable({
  providedIn: 'root'
})
export class RoleGuard implements CanActivate {
  constructor(private authService: Role, private router: Router) {}

  canActivate(
    route: ActivatedRouteSnapshot,
    state: RouterStateSnapshot): boolean {
    const expectedRoles = route.data['roles'] as Array<string>;
    const hasRole = expectedRoles.some(role => this.authService.hasRole(role));

    if (!hasRole) {
      this.router.navigate(['access-denied']); // Navigate to an access denied page or any other route
      return false;
    }
    return true;
  }
}
@Injectable({
  providedIn: 'root'
})
export class AuthService {
 
  constructor(private keycloakService: KeycloakService) {

  }

  getLoggedUser(): KeycloakTokenParsed | undefined{
    try {
      return this.keycloakService.getKeycloakInstance().idTokenParsed;
    } catch (e) {
      return undefined
    }
  }

  isLoggedIn(){
    return true;
  }

  getToken(){
    return this.keycloakService.getKeycloakInstance().token;
  }

  loadUserProfile() {
    return this.keycloakService.loadUserProfile();
  }

  login(){
    this.keycloakService.login();
  }

  logout(){
    this.keycloakService.logout();
  }

  redirectToProfile(){
    this.keycloakService.getKeycloakInstance().accountManagement();
  }

  refreshToken(){
    this.keycloakService.keycloakEvents$.subscribe({
      next: (e) => {
    
        if (String(e.type) === KeycloakEventType.TokenExpired) {
          this.keycloakService.updateToken(20);
        }
      }
    });
  }

}
hey chat i have this probleme is when i tab aurl exist like
http://localhost:4200/pages/reporting i pass in login page i connet with a user that have access to this url but it redirect me vers not-found page thsi happen when i connect on url after the first login
