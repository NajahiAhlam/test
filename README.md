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
      map(() => true), // ✅ router waits until this emits
      catchError(err => {
        console.error('Error loading user in resolver:', err);
        sessionStorage.setItem('roles', JSON.stringify([]));
        return of(true);
      })
    );
  }
}
