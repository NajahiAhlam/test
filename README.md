<nb-card class="metrics-card">
  <nb-card-body>
    <div class="metrics-row" *ngFor="let metric of metrics">
      <nb-icon [icon]="metric.icon" pack="fa" class="metrics-icon"></nb-icon>
      <div class="metrics-info">
        <div class="metrics-label">{{ metric.label }}</div>
        <div class="metrics-value">{{ metric.value | percent }}</div>
      </div>
    </div>
  </nb-card-body>
</nb-card>
