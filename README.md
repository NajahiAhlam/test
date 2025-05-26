  updateCondition(condition?: ConditionsDTO) {
    this.dialogService.open(UpdateConditionDialogComponent,{
      context:{
        data: condition
      },
      hasBackdrop: false,
        setTimeout(() => {
  document.body.classList.remove('nb-global-scrollblock');
}, 100)
    })
  
    .onClose.subscribe((data:Risque)=>{
        if(data){
          this.fetchData()
        }
      })
  }
