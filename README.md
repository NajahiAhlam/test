updateCondition(condition?: ConditionsDTO) {
  const dialogRef = this.dialogService.open(UpdateConditionDialogComponent, {
    context: {
      data: condition
    },
    hasBackdrop: false
  });

  // Remove scroll-block class shortly after the dialog opens
  setTimeout(() => {
    document.body.classList.remove('nb-global-scrollblock');
  }, 100);

  dialogRef.onClose.subscribe((data: Risque) => {
    if (data) {
      this.fetchData();
    }
  });
}
