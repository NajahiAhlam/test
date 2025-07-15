@Injectable({ providedIn: 'root' })
export class AppResolver implements Resolve<User> {
  constructor(
    private authService: AuthService,
    private usersService: UserService
  ) {}

  resolve(
    route: ActivatedRouteSnapshot,
    state: RouterStateSnapshot
  ): Observable<User> {
    const email = this.authService.getLoggedUser()?.['email'];

    return this.usersService.getByUsername(email).pipe(
      tap((user: User) => {
        if (user.roles) {
          sessionStorage.setItem('roles', JSON.stringify(user.roles));
        } else {
          sessionStorage.setItem('roles', JSON.stringify([]));
        }
      })
    );
  }
}
