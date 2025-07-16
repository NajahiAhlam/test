canActivate(
  route: ActivatedRouteSnapshot,
  state: RouterStateSnapshot
): boolean {
  const expectedRoles = route.data['roles'] as Array<string>;

  const rolesRaw = sessionStorage.getItem('roles');
  if (!rolesRaw) {
    console.warn("RoleGuard: No roles found in sessionStorage!");
    // Optionally redirect or just block access
    this.router.navigate(['access-denied']);
    return false;
  }

  const roles = JSON.parse(rolesRaw);
  const hasRole = expectedRoles.some(role => roles.includes(role));

  console.log("RoleGuard → expected:", expectedRoles, " | actual roles:", roles);

  if (!hasRole) {
    this.router.navigate(['access-denied']);
    return false;
  }

  return true;
}
