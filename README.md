@Injectable({ providedIn: 'root' })
export class RoleService {
  private rolesSubject = new BehaviorSubject<string[] | null>(null);

  constructor(private userService: UserService, private auth: AuthService) {}

  loadRoles(): Observable<string[]> {
    if (this.rolesSubject.value !== null) {
      return this.rolesSubject.asObservable(); // already loaded
    }

    const email = this.auth.getLoggedUser()?.email;

    return this.userService.getByUsername(email).pipe(
      tap(user => {
        const roles = user.roles || [];
        sessionStorage.setItem('roles', JSON.stringify(roles));
        this.rolesSubject.next(roles);
      }),
      map(user => user.roles || []),
      catchError(err => {
        console.error('Error loading roles', err);
        sessionStorage.setItem('roles', '[]');
        this.rolesSubject.next([]);
        return of([]);
      })
    );
  }

  getRoles(): Observable<string[]> {
    const fromStorage = sessionStorage.getItem('roles');
    if (fromStorage && this.rolesSubject.value === null) {
      const parsed = JSON.parse(fromStorage);
      this.rolesSubject.next(parsed);
    }
    return this.rolesSubject.asObservable();
  }

  hasRole(role: string): boolean {
    const roles = this.rolesSubject.value || JSON.parse(sessionStorage.getItem('roles') || '[]');
    return roles.includes(role);
  }
}
@Injectable({ providedIn: 'root' })
export class RoleGuard implements CanActivate {
  constructor(private roleService: RoleService, private router: Router) {}

  canActivate(route: ActivatedRouteSnapshot, state: RouterStateSnapshot): Observable<boolean> {
    const expectedRoles = route.data['roles'] as string[];

    return this.roleService.loadRoles().pipe(
      map(roles => {
        const hasRole = expectedRoles.some(role => roles.includes(role));
        console.log("RoleGuard → expected:", expectedRoles, " | actual:", roles);

        if (!hasRole) {
          this.router.navigate(['access-denied']);
          return false;
        }

        return true;
      }),
      catchError(() => {
        this.router.navigate(['access-denied']);
        return of(false);
      })
    );
  }
}
