Perfect \U0001f44d \u2014 you already have a good base.
Now you just need to **redirect the user to the login page** when:
1\ufe0f\u20e3 the session expires (token invalid / 401 response)
2\ufe0f\u20e3 the user clicks logout.

Let\u2019s handle both cases cleanly \U0001f447

---

### \u2705 1. Redirect to login on logout

You can modify your `logout()` like this:

```typescript
logout() {
    const logoutAPI = "/itf-ms-access-authenticate/token/logout";
    this.baseService.post(logoutAPI, {}).subscribe({
        next: () => {
            this.isAuthenticatedSubject.next(false);
            this.persisteAuthState(false, []);

            // Clear local storage/session
            localStorage.clear();
            sessionStorage.clear();

            // Redirect to login page
            if (environment.envType === EnvironmentType.BACKOFFICE) {
                document.location.href = environment.authProviderLogoutUrl;
            } else {
                this.router.navigate(['/login']);
            }
        },
        error: () => {
            // Still redirect even if logout API fails
            this.router.navigate(['/login']);
        }
    });
}
```

> \u2699\ufe0f Make sure your service has `private router: Router` injected.

---

### \u2705 2. Redirect to login on session expiration

To handle expired tokens globally, use an **HTTP interceptor**.

**Create:** `auth.interceptor.ts`

```typescript
import { Injectable } from '@angular/core';
import {
  HttpEvent,
  HttpHandler,
  HttpInterceptor,
  HttpRequest,
  HttpErrorResponse
} from '@angular/common/http';
import { Observable, throwError } from 'rxjs';
import { catchError } from 'rxjs/operators';
import { Router } from '@angular/router';

@Injectable()
export class AuthInterceptor implements HttpInterceptor {
  constructor(private router: Router) {}

  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    return next.handle(req).pipe(
      catchError((error: HttpErrorResponse) => {
        // If token expired or unauthorized
        if (error.status === 401 || error.status === 403) {
          localStorage.clear();
          sessionStorage.clear();
          this.router.navigate(['/login']);
        }
        return throwError(() => error);
      })
    );
  }
}
```

Then **register it** in your `app.module.ts`:

```typescript
import { HTTP_INTERCEPTORS } from '@angular/common/http';
import { AuthInterceptor } from './interceptors/auth.interceptor';

@NgModule({
  providers: [
    {
      provide: HTTP_INTERCEPTORS,
      useClass: AuthInterceptor,
      multi: true
    }
  ]
})
export class AppModule {}
```

---

### \u2705 3. Optional: add route guard

To prevent unauthorized access after logout or session expiration, add an **AuthGuard**.

```typescript
import { Injectable } from '@angular/core';
import { CanActivate, Router } from '@angular/router';

@Injectable({ providedIn: 'root' })
export class AuthGuard implements CanActivate {
  constructor(private router: Router) {}

  canActivate(): boolean {
    const token = localStorage.getItem('token');
    if (!token) {
      this.router.navigate(['/login']);
      return false;
    }
    return true;
  }
}
```

And protect your routes:

```typescript
{
  path: 'dashboard',
  component: DashboardComponent,
  canActivate: [AuthGuard]
}
```

---

### \u2705 Summary

| Situation                            | What happens                                |
| ------------------------------------ | ------------------------------------------- |
| User logs out                        | Clear storage \u2192 redirect to login           |
| Token/session expired                | Interceptor catches 401 \u2192 redirect to login |
| User tries to access protected route | Guard redirects to login if no token        |

---

Would you like me to adapt this setup to your **current `baseService`** (since it seems you already wrap HTTP calls there)? I can integrate the 401 handling directly in it if you prefer a single place to manage it.
