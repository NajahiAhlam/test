checkPopupCondition(): void {
  const hasQ1 = Array.from(this.checkedQuestions).some(q => q === "1" || q.startsWith("1."));
  const hasAnother = Array.from(this.checkedQuestions).some(q => {
    if (q === "1" || q.startsWith("1.")) return false;
    const num = parseInt(q.split('.')[0]);
    return num >= 1 && num <= 5;
  });

  this.showAdditionalContent = hasQ1 && hasAnother;
}
toggleResponse(question: any): void {
  question.response = !question.response;

  if (question.response) {
    this.checkedQuestions.add(question.numQuestion);
  } else {
    this.checkedQuestions.delete(question.numQuestion);
  }

  this.checkPopupCondition();
  setTimeout(() => this.cdr.detectChanges(), 0);
}
