@Injectable({ providedIn: 'root' })
export class AppResolver {
  constructor(private authService: AuthService, private usersService: UserService) {}

  resolve(
    route: ActivatedRouteSnapshot,
    state: RouterStateSnapshot
  ): Observable<User> {
       return this.usersService.getByUsername(this.authService.getLoggedUser()?.['email']);

  }



}
