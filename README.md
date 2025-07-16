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

    
console.log("expectedRoles: ", expectedRoles)


    if (!hasRole) {
      this.router.navigate(['access-denied']); // Navigate to an access denied page or any other route
      return false;
    }
    return true;
  }
}
import { Injectable } from '@angular/core';

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

@Injectable({ providedIn: 'root' })
export class AppResolver implements Resolve<boolean> {
 constructor(
 private authService: AuthService,
 private usersService: UserService
 ) {}

 resolve(
 route: ActivatedRouteSnapshot,
 state: RouterStateSnapshot
 ): Observable<boolean> {
 const email = this.authService.getLoggedUser()?.['email'];

return this.usersService.getByUsername(email).pipe(
 tap((user: User) => {
 console.log('User loaded in resolver:', user);
 sessionStorage.setItem('roles', JSON.stringify(user.roles || []));
 }),
 map(() => true), 
catchError(err => {
 console.error('Error loading user in resolver:', err);
 sessionStorage.setItem('roles', JSON.stringify([]));
 return of(true);
 })
 );
 }
}
const routes: Routes = [
  {
   path: 'pages',
   loadChildren: () => import('./pages/pages.module')
     .then(m => m.PagesModule),
     resolve: {
      initData: AppResolver
    },

  },
  { path: '', redirectTo: 'pages/demande', pathMatch: 'full' },

  { path: '**', redirectTo: 'pages/not-found' },
];

@NgModule({
  imports: [RouterModule.forRoot(routes, {onSameUrlNavigation: 'reload', initialNavigation: 'enabledBlocking'})],
  exports: [RouterModule],
  providers: [AppResolver]
})
export class AppRoutingModule { }
const routes: Routes = [
  {
    title: "MYCNP",
    path: '',
    component: PagesComponent,
    children: [
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
        path: 'referentiel/risques',
        component: RisqueListComponent,
        canActivate: [RoleGuard],

        data: {
          roles: ['CoordinateurCNP']
        }
      },
      {
          path: 'demande',
          component: ListDemandeComponent,
          canActivate: [RoleGuard],

          data: {
            roles: ['CoordinateurCNP','Leader','SponsorM',"Validateur"]
          }
      },
        {
          path: 'produit',
          component: ProduitListComponent,
          canActivate: [RoleGuard],

          data: {
            roles: ['CoordinateurCNP','Leader','SponsorM',"Validateur"]
          }
      },
      {
          path: 'reporting',
          component: PilotageConditionsComponent,
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
        path: '**',  pathMatch: 'full', 
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
hey i have a probleme is when i tab url in frist login after login it show page notFound



