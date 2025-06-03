isRequiredQuestionsConditionMet(): boolean {
  const responses = this.demande?.demandeQualificationQuestionReponses || [];

  const q1 = responses.find(q => q.parentQuestion?.numQuestion?.toString() === '1');
  if (!q1 || !q1.answered) return false;

  // Look for at least one other parent (2-5) or any sub-question of them answered
  const othersAnswered = responses.some(q => {
    const parentNum = q.parentQuestion?.numQuestion?.toString();
    return ['2', '3', '4', '5'].includes(parentNum) && q.answered;
  });

  const subQuestionAnswered = ['2', '3', '4', '5'].some(parentNum =>
    this.getSubQuestions(parentNum).some(sub => sub.answered)
  );

  return othersAnswered || subQuestionAnswered;
}
getParentQuestions(): any[] {
  const responses = this.demande?.demandeQualificationQuestionReponses || [];
  return responses
    .filter((q, index, self) =>
      q.parentQuestion &&
      self.findIndex(p => p.parentQuestion.numQuestion === q.parentQuestion.numQuestion) === index &&
      (this.isRequiredQuestionsConditionMet() ? true : parseInt(q.parentQuestion.numQuestion) <= 5)
    )
    .sort((a, b) =>
      parseFloat(a.parentQuestion.numQuestion.toString()) - parseFloat(b.parentQuestion.numQuestion.toString())
    ) || [];
}
updateAnswer(item: any, event: Event): void {
  item.answered = (event.target as HTMLInputElement).checked;
  this.cdr.detectChanges();
}

