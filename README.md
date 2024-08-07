<nb-layout>
  <nb-layout-header fixed>
    <nb-actions>
      <nb-action>
        <nb-icon icon="nb-lightbulb-outline"></nb-icon>
        <span>Project Stats</span>
      </nb-action>
    </nb-actions>
  </nb-layout-header>

  <nb-layout-column>
    <nb-card>
      <nb-card-header>Project Phase Counts</nb-card-header>
      <nb-card-body>
        <nb-list>
          <nb-list-item>
            <nb-icon icon="nb-compose"></nb-icon>
            <span class="data-label">Non Lances:</span>
            <h4 class="data-value">{{ aggregatedData?.NonLances || 0 }}</h4>
          </nb-list-item>
          <nb-list-item>
            <nb-icon icon="nb-reload"></nb-icon>
            <span class="data-label">En Cadrage:</span>
            <h4 class="data-value">{{ aggregatedData?.EnCadrage || 0 }}</h4>
          </nb-list-item>
          <nb-list-item>
            <nb-icon icon="nb-bar-chart"></nb-icon>
            <span class="data-label">En Realisation:</span>
            <h4 class="data-value">{{ aggregatedData?.EnRealisation || 0 }}</h4>
          </nb-list-item>
          <nb-list-item>
            <nb-icon icon="nb-arrow-up"></nb-icon>
            <span class="data-label">Preclotures:</span>
            <h4 class="data-value">{{ aggregatedData?.Preclotures || 0 }}</h4>
          </nb-list-item>
          <nb-list-item>
            <nb-icon icon="nb-arrow-down"></nb-icon>
            <span class="data-label">Clotures:</span>
            <h4 class="data-value">{{ aggregatedData?.Clotures || 0 }}</h4>
          </nb-list-item>
          <nb-list-item>
            <nb-icon icon="nb-cloud"></nb-icon>
            <span class="data-label">Abandonnees:</span>
            <h4 class="data-value">{{ aggregatedData?.Abandonnees || 0 }}</h4>
          </nb-list-item>
          <nb-list-item>
            <nb-icon icon="nb-signal"></nb-icon>
            <span class="data-label">Total Projects:</span>
            <h4 class="data-value">{{ aggregatedData?.countProjet || 0 }}</h4>
          </nb-list-item>
        </nb-list>
      </nb-card-body>
    </nb-card>
  </nb-layout-column>
</nb-layout>
.data-label {
  font-weight: bold;
  margin-right: 10px;
}

.data-value {
  color: #2d8cf0;
  font-size: 1.2rem;
  margin: 0;
}

nb-list-item {
  display: flex;
  align-items: center;
  padding: 10px 0;
}

nb-icon {
  margin-right: 15px;
}

nb-card {
  margin: 20px;
}

nb-card-header {
  background-color: #f4f4f4;
  border-bottom: 1px solid #e0e0e0;
  font-weight: bold;
}
