Perfect \U0001f44c you\u2019re **very close** \u2014 the issue now is purely **timing and data refresh**:

When you close your import window, your subscription in

```ts
.onClose.subscribe((data) => { this.getAllArticles() })
```

is being triggered *before* the backend has actually finished saving the imported data.
So the new list is fetched too early \u2192 you only see headers until you manually refresh the page.

Let\u2019s fix that cleanly \U0001f447

---

### \u2705 Option 1 \u2014 Delay refresh a bit (simple & safe)

Add a small delay before calling `getAllArticles()` to give the backend time to save:

```ts
import() {
  const buttonsConfig: NbWindowControlButtonsConfig = {
    minimize: true,
    maximize: false,
    fullScreen: false,
    close: true,
  };

  this.windowService
    .open(ArticleInjectionFormComponent, {
      closeOnBackdropClick: false,
      buttons: buttonsConfig,
    })
    .onClose.subscribe(() => {
      setTimeout(() => {
        this.getAllArticles();
      }, 1000); // wait 1 second before refreshing
    });
}
```

\U0001f7e2 Works instantly and avoids extra complexity.

---

### \u2705 Option 2 \u2014 Refresh only when backend confirms success (best practice)

If your `ArticleInjectionFormComponent` emits a success event when import finishes (e.g. via `NbDialogRef`\u2019s `close('success')`), you can check it explicitly:

```ts
this.windowService
  .open(ArticleInjectionFormComponent, {
    closeOnBackdropClick: false,
  })
  .onClose.subscribe((result) => {
    if (result === 'success') {
      this.getAllArticles();
    }
  });
```

And inside your import component:

```ts
constructor(private dialogRef: NbDialogRef<ArticleInjectionFormComponent>) {}

onImportSuccess() {
  this.dialogRef.close('success');
}
```

\u2705 That ensures the refresh happens **only when import has truly succeeded**, not just when the window closes.

---

### \u2705 Option 3 \u2014 Visual loading refresh (optional improvement)

Add a temporary loader for better UX:

```ts
this.loading = true;
setTimeout(() => {
  this.getAllArticles();
  this.loading = false;
}, 1000);
```

---

\u27a1\ufe0f **Recommendation:**
Use **Option 2** (emit `'success'` when import done).
If you can\u2019t modify the import component easily, use **Option 1** with a small delay (1\u20132 seconds).

---

Would you like me to show how to add that `close('success')` emission inside your `ArticleInjectionFormComponent` (so it works in a clean way)?
