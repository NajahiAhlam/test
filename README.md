<div class="key-value-pair" *ngIf="reporterNames">
  <div class="key col-6">Initiateur:</div>
  <div class="value col-6" [innerHTML]="getHighlightedNames(reporterNames, data?.lead | emailToName)"></div>
</div>

<div class="key-value-pair" *ngIf="cordinateurNames">
  <div class="key col-6">Coordinateur:</div>
  <div class="value col-6" [innerHTML]="getHighlightedNames(cordinateurNames, data?.assigneeName)"></div>
</div>

<div class="key-value-pair" *ngIf="sponsorNames">
  <div class="key col-6">Sponsor:</div>
  <div class="value col-6" [innerHTML]="getHighlightedNames(sponsorNames, data?.sponsor)"></div>
</div>
getHighlightedNames(names: string, activeName: string): string {
  if (!names) return '';
  const nameList = names.split(',').map(n => n.trim());

  return nameList
    .map(name => {
      const isActive = name === activeName;
      return isActive
        ? `<span style="color: green; font-weight: bold;">${name}</span>`
        : name;
    })
    .join(', ');
}
