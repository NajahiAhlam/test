/* This ensures scroll block does not impact layout height */
body.cdk-global-scrollblock,
body.nb-global-scrollblock {
  overflow: hidden !important;
  position: relative; /* prevent layout shift */
}
.cdk-overlay-container {
  position: fixed;
  z-index: 1000; /* or higher if needed */
  pointer-events: none;
}

.cdk-overlay-pane {
  pointer-events: auto;
}
