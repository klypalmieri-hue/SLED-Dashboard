# SLED-Dashboard
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta name="artifact-type" content="dashboard">
  <meta name="design-style" content="briefing">
  <title>2026 Post-Election SLED Market Dashboard</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Source+Serif+4:ital,opsz,wght@0,8..60,400;0,8..60,600;0,8..60,700;1,8..60,400&family=Plus+Jakarta+Sans:wght@400;500;600;700&family=Geist+Mono:wght@400;500;600&display=swap" rel="stylesheet">
  <script src="https://cdn.jsdelivr.net/npm/chart.js@4.5.1/dist/chart.umd.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/plotly.js-dist-min@2.32.0/plotly.min.js"></script>
  <script src="https://unpkg.com/lucide@1.43.0/dist/umd/lucide.min.js"></script>
  <style>
    :root {
      color-scheme: light;

      /* Page and surfaces: warm stone-white paper */
      --background: #faf9f5;
      --surface-container-low: #ffffff;
      --surface-container: #f1efe8;
      --surface-container-high: #e6e2d6;

      /* Ink */
      --on-surface: #17181a;
      --on-surface-variant: #56585c;

      /* Rules */
      --outline: #17181a;
      --outline-variant: #dedad0;

      /* Accents */
      --primary: #17181a;
      --on-primary: #faf9f5;
      --primary-container: #f2b705;
      --on-primary-container: #17181a;
      --secondary: #1f4e5f;
      --tertiary: #785008;
      --error: #b42318;
      --on-error: #ffffff;

      /* Tag & status surface tokens */
      --tag-warm-bg: #f5e6c4;
      --tag-slate-bg: #e3edf0;
      --tag-pine-bg: #e1efe8;
      --tag-brick-bg: #f7e4e1;
      --btn-secondary-border: #c8c4b8;
      --map-neutral: #e8e4da;
      --map-border: #ffffff;
      --highlight-soft: rgba(242, 183, 5, 0.16);
      --focus-ring-shadow: 0 0 0 3px rgba(31, 78, 95, 0.25);

      /* Data series, in order of use */
      --data-1: #1f4e5f;
      --data-2: #d99000;
      --data-3: #2b6e52;
      --data-4: #b84a39;
      --data-context: #c4bfb2;

      /* Fonts */
      --font-headline: 'Source Serif 4', serif;
      --font-body: 'Plus Jakarta Sans', sans-serif;
      --font-label: 'Geist Mono', monospace;

      /* Shape */
      --rounded: 0.25rem;
      --rounded-lg: 0.375rem;
      --rounded-full: 0.25rem;

      /* Style effects */
      --section-rule: 1.5px solid var(--outline);
      --card-border: 1px solid var(--outline-variant);
      --badge-radius: 2px;
      --heading-tracking: -0.02em;
      --label-tracking: 0.08em;

      /* Shared semantic & focus tokens */
      --positive: #2b6e52;
      --warning: #785008;
      --focus: #1f4e5f;

      /* Type scale: Dashboard / Data Explorer */
      --display-size: clamp(1.75rem, 1.4rem + 1.2vw, 2.25rem);
      --headline-size: clamp(1.25rem, 1.1rem + 0.5vw, 1.5rem);
      --subhead-size: clamp(1.0625rem, 1rem + 0.25vw, 1.1875rem);
      --body-size: clamp(0.9375rem, 0.9rem + 0.15vw, 1rem);
      --label-size: 0.75rem;

      /* Spacing scale (4pt base) */
      --space-xs: 0.25rem;
      --space-sm: 0.5rem;
      --space-md: 1rem;
      --space-lg: 1.5rem;
      --space-xl: 2.5rem;
      --space-2xl: 4rem;
      --space-3xl: 6rem;

      /* Motion */
      --ease-out: cubic-bezier(0.16, 1, 0.3, 1);
      --ease-in-out: cubic-bezier(0.65, 0, 0.35, 1);
      --dur-micro: 120ms;
      --dur-short: 220ms;
      --dur-long: 420ms;
    }

    /* Base reset */
    *, *::before, *::after { box-sizing: border-box; }
    html, body { overflow-x: clip; margin: 0; padding: 0; }
    header { position: static !important; }

    body {
      background: var(--background);
      color: var(--on-surface);
      font-family: var(--font-body);
      font-size: var(--body-size);
      line-height: 1.55;
      -webkit-font-smoothing: antialiased;
    }

    .page-grid {
      display: grid;
      grid-template-columns: minmax(0, 1fr);
      gap: var(--space-xl);
      padding: var(--space-lg);
      max-width: 1280px;
      margin: 0 auto;
      overflow: hidden;
    }
    .page-grid * { min-width: 0; }

    /* Focus rings */
    :focus-visible {
      outline: 2px solid var(--focus);
      outline-offset: 2px;
    }

    /* Briefing Defining Treatments */
    .dossier-bar {
      display: flex;
      flex-wrap: wrap;
      align-items: center;
      justify-content: space-between;
      gap: var(--space-sm);
      border-bottom: var(--section-rule);
      padding-bottom: var(--space-sm);
      margin-bottom: var(--space-md);
    }

    .dossier-bar-left {
      display: flex;
      flex-wrap: wrap;
      align-items: center;
      gap: var(--space-sm);
    }

    .dossier-pill {
      background: var(--primary-container);
      color: var(--on-primary-container);
      font-family: var(--font-body);
      font-weight: 700;
      font-size: 0.6875rem;
      letter-spacing: var(--label-tracking);
      text-transform: uppercase;
      padding: 0.25rem 0.5rem;
      border-radius: var(--badge-radius);
      display: inline-flex;
      align-items: center;
      gap: var(--space-xs);
    }

    .dossier-meta {
      font-family: var(--font-label);
      font-size: var(--label-size);
      color: var(--on-surface-variant);
      text-transform: uppercase;
      letter-spacing: 0.06em;
    }

    .title {
      font-family: var(--font-headline);
      font-weight: 600;
      font-size: var(--display-size);
      letter-spacing: var(--heading-tracking);
      line-height: 1.08;
      color: var(--on-surface);
      margin: 0;
    }

    .section-heading {
      font-family: var(--font-headline);
      font-weight: 600;
      font-size: var(--headline-size);
      letter-spacing: var(--heading-tracking);
      line-height: 1.15;
      color: var(--on-surface);
      margin: 0 0 var(--space-xs) 0;
    }

    .subhead {
      font-family: var(--font-headline);
      font-weight: 600;
      font-size: var(--subhead-size);
      letter-spacing: -0.01em;
      line-height: 1.25;
      color: var(--on-surface);
      margin: 0;
    }

    .standfirst {
      font-family: var(--font-body);
      font-weight: 600;
      font-size: var(--subhead-size);
      color: var(--secondary);
      margin: var(--space-sm) 0 0 0;
      max-width: 72ch;
      line-height: 1.4;
    }

    .section-deck {
      font-family: var(--font-body);
      font-weight: 500;
      color: var(--on-surface-variant);
      margin: 0 0 var(--space-md) 0;
      max-width: 75ch;
    }

    .figure-card {
      background: var(--surface-container-low);
      border: var(--card-border);
      border-radius: var(--rounded-lg);
      padding: var(--space-md) var(--space-lg);
      transition: background-color var(--dur-micro) var(--ease-out);
    }

    .figure-card .value {
      font-family: var(--font-headline);
      font-weight: 600;
      font-variant-numeric: lining-nums tabular-nums;
      color: var(--on-surface);
      line-height: 1.08;
    }

    .index-num {
      font-family: var(--font-label);
      font-weight: 600;
      font-size: var(--label-size);
      color: var(--secondary);
      letter-spacing: 0.06em;
    }

    .aside-box {
      background: var(--surface-container);
      border-top: var(--section-rule);
      padding: var(--space-md) var(--space-lg);
      border-radius: 0 0 var(--rounded-lg) var(--rounded-lg);
    }

    .source-note {
      font-family: var(--font-label);
      font-size: var(--label-size);
      color: var(--on-surface-variant);
      margin-top: var(--space-sm);
      line-height: 1.45;
    }

    /* Header & Jump Navigation */
    .header-grid {
      display: grid;
      grid-template-columns: minmax(0, 1.8fr) minmax(0, 1fr);
      gap: var(--space-lg);
      align-items: end;
    }

    .header-status-box {
      background: var(--surface-container);
      border-top: var(--section-rule);
      padding: var(--space-md);
      display: flex;
      flex-direction: column;
      gap: var(--space-xs);
    }

    .jump-nav {
      display: flex;
      flex-wrap: wrap;
      align-items: center;
      gap: var(--space-sm);
      padding: var(--space-sm) 0;
      border-top: 1px solid var(--outline-variant);
      border-bottom: 1px solid var(--outline-variant);
      margin-top: var(--space-md);
    }

    .jump-link {
      display: inline-flex;
      align-items: center;
      gap: var(--space-xs);
      min-height: 44px;
      padding: var(--space-xs) var(--space-md);
      border-radius: var(--rounded);
      border: 1px solid var(--btn-secondary-border);
      background: var(--surface-container-low);
      color: var(--on-surface);
      text-decoration: none;
      font-family: var(--font-body);
      font-weight: 600;
      font-size: 0.875rem;
      transition: background-color var(--dur-micro) var(--ease-out), color var(--dur-micro) var(--ease-out);
    }

    .jump-link:hover {
      background: var(--surface-container-high);
    }

    /* KPI Strip */
    .kpi-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(min(210px, 100%), 1fr));
      gap: var(--space-md);
    }

    .kpi-card {
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      gap: var(--space-xs);
      border-top: 3px solid var(--secondary);
    }

    .kpi-card.kpi-gold {
      border-top-color: var(--primary-container);
    }

    .kpi-label {
      font-family: var(--font-label);
      font-size: var(--label-size);
      text-transform: uppercase;
      letter-spacing: 0.06em;
      color: var(--on-surface-variant);
    }

    .kpi-value {
      font-size: clamp(1.75rem, 1.4rem + 1vw, 2.25rem);
      margin: var(--space-xs) 0;
    }

    .kpi-detail {
      font-size: 0.875rem;
      color: var(--on-surface-variant);
      line-height: 1.4;
    }

    .kpi-delta {
      font-family: var(--font-label);
      font-size: var(--label-size);
      font-weight: 600;
      color: var(--tertiary);
    }

    /* Buttons & Controls */
    .btn {
      display: inline-flex;
      align-items: center;
      justify-content: center;
      gap: var(--space-xs);
      min-height: 44px;
      padding: var(--space-xs) var(--space-md);
      border-radius: var(--rounded);
      font-family: var(--font-body);
      font-weight: 600;
      font-size: 0.875rem;
      cursor: pointer;
      border: 1px solid transparent;
      transition: background-color var(--dur-micro) var(--ease-out), box-shadow var(--dur-micro) var(--ease-out), color var(--dur-micro) var(--ease-out);
    }

    .btn-primary {
      background: var(--primary);
      color: var(--on-primary);
    }

    .btn-primary:hover {
      background: var(--secondary);
    }

    .btn-secondary {
      background: var(--surface-container-low);
      color: var(--on-surface);
      border-color: var(--btn-secondary-border);
    }

    .btn-secondary:hover {
      background: var(--surface-container-high);
    }

    .btn-secondary[aria-pressed="true"],
    .filter-chip[aria-pressed="true"] {
      background: var(--primary);
      color: var(--on-primary);
      border-color: var(--primary);
    }

    .filter-row {
      display: flex;
      flex-wrap: wrap;
      align-items: end;
      gap: var(--space-sm);
      margin-bottom: var(--space-md);
    }

    .field-group {
      display: flex;
      flex-direction: column;
      gap: var(--space-xs);
    }

    .field-label {
      font-family: var(--font-label);
      font-size: var(--label-size);
      font-weight: 500;
      text-transform: uppercase;
      letter-spacing: 0.06em;
      color: var(--on-surface-variant);
    }

    .input-control,
    .select-control {
      min-height: 44px;
      padding: var(--space-xs) var(--space-md);
      border: 1px solid var(--btn-secondary-border);
      border-radius: var(--rounded);
      background: var(--surface-container-low);
      color: var(--on-surface);
      font-family: var(--font-body);
      font-size: 0.9375rem;
    }

    .select-control {
      padding-right: var(--space-xl);
    }

    .input-control:focus,
    .select-control:focus {
      outline: none;
      box-shadow: var(--focus-ring-shadow);
      border-color: var(--secondary);
    }

    /* Tags & Chips */
    .tag {
      display: inline-flex;
      align-items: center;
      gap: var(--space-xs);
      font-family: var(--font-label);
      font-size: var(--label-size);
      font-weight: 500;
      padding: 0.1875rem 0.45rem;
      border-radius: var(--rounded-full);
      text-transform: uppercase;
      letter-spacing: 0.04em;
      white-space: nowrap;
    }

    .tag-warm { background: var(--tag-warm-bg); color: var(--tertiary); }
    .tag-slate { background: var(--tag-slate-bg); color: var(--secondary); }
    .tag-pine { background: var(--tag-pine-bg); color: var(--positive); }
    .tag-brick { background: var(--tag-brick-bg); color: var(--error); }
    .tag-gold { background: var(--primary-container); color: var(--on-primary-container); font-weight: 700; }

    /* Two-column analytical panels */
    .split-layout {
      display: grid;
      grid-template-columns: minmax(0, 1.45fr) minmax(0, 1fr);
      gap: var(--space-lg);
      align-items: start;
    }

    .equal-grid-2 {
      display: grid;
      grid-template-columns: repeat(2, minmax(0, 1fr));
      gap: var(--space-lg);
    }

    .card-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(min(270px, 100%), 1fr));
      gap: var(--space-md);
    }

    /* Map & Chart containers */
    .map-container {
      min-height: 380px;
      width: 100%;
    }

    .chart-container {
      position: relative;
      min-height: 270px;
      width: 100%;
    }

    .state-pill-grid {
      display: flex;
      flex-wrap: wrap;
      gap: var(--space-xs);
      margin-top: var(--space-sm);
    }

    .state-pill {
      min-height: 44px;
      min-width: 48px;
      padding: var(--space-xs) var(--space-sm);
      border-radius: var(--rounded);
      border: 1px solid var(--outline-variant);
      background: var(--surface-container-low);
      font-family: var(--font-label);
      font-size: var(--label-size);
      font-weight: 600;
      color: var(--on-surface);
      cursor: pointer;
      display: inline-flex;
      align-items: center;
      justify-content: center;
      gap: var(--space-xs);
      transition: background-color var(--dur-micro) var(--ease-out), border-color var(--dur-micro) var(--ease-out);
    }

    .state-pill:hover {
      background: var(--surface-container-high);
    }

    .state-pill.active {
      background: var(--secondary);
      color: var(--on-primary);
      border-color: var(--secondary);
    }

    .legend-row {
      display: flex;
      flex-wrap: wrap;
      gap: var(--space-md);
      margin-top: var(--space-sm);
      font-family: var(--font-label);
      font-size: var(--label-size);
      color: var(--on-surface-variant);
    }

    .legend-item {
      display: inline-flex;
      align-items: center;
      gap: var(--space-xs);
    }

    .legend-swatch {
      width: 12px;
      height: 12px;
      border-radius: var(--badge-radius);
      display: inline-block;
      flex-shrink: 0;
    }

    /* Tables */
    .table-wrap {
      overflow-x: auto;
      width: 100%;
    }

    table.briefing-table {
      width: 100%;
      border-collapse: collapse;
      background: var(--surface-container-low);
      font-size: 0.9rem;
    }

    table.briefing-table th {
      text-align: left;
      font-family: var(--font-label);
      font-size: var(--label-size);
      font-weight: 600;
      text-transform: uppercase;
      letter-spacing: 0.06em;
      color: var(--on-surface-variant);
      padding: var(--space-sm) var(--space-md);
      border-bottom: var(--section-rule);
      background: var(--surface-container-low);
      white-space: nowrap;
    }

    table.briefing-table td {
      padding: var(--space-sm) var(--space-md);
      border-bottom: 1px solid var(--outline-variant);
      vertical-align: top;
      color: var(--on-surface);
    }

    table.briefing-table tbody tr:hover {
      background: var(--surface-container);
    }

    table.briefing-table tbody tr.selected-row {
      background: var(--highlight-soft);
    }

    .num-cell {
      font-family: var(--font-label);
      font-variant-numeric: tabular-nums;
      white-space: nowrap;
    }

    .sort-btn {
      background: none;
      border: none;
      padding: 0;
      font: inherit;
      color: inherit;
      text-transform: inherit;
      letter-spacing: inherit;
      cursor: pointer;
      display: inline-flex;
      align-items: center;
      gap: var(--space-xs);
      min-height: 44px;
    }

    /* 21 Governor Matrix */
    .matrix-grid {
      display: grid;
      grid-template-columns: 155px repeat(3, minmax(0, 1fr));
      gap: var(--space-xs);
      margin-top: var(--space-sm);
    }

    .matrix-head {
      font-family: var(--font-label);
      font-size: var(--label-size);
      font-weight: 600;
      text-transform: uppercase;
      color: var(--secondary);
      padding: var(--space-sm);
      border-bottom: var(--section-rule);
    }

    .matrix-row-label {
      font-family: var(--font-label);
      font-size: var(--label-size);
      font-weight: 600;
      color: var(--on-surface);
      padding: var(--space-sm);
      background: var(--surface-container);
      border-radius: var(--rounded);
      display: flex;
      flex-direction: column;
      justify-content: center;
      gap: var(--space-xs);
    }

    .matrix-cell {
      background: var(--surface-container-low);
      border: 1px solid var(--outline-variant);
      border-radius: var(--rounded);
      padding: var(--space-sm);
      min-height: 76px;
      display: flex;
      flex-wrap: wrap;
      align-content: flex-start;
      gap: var(--space-xs);
    }

    .matrix-cell.empty-cell {
      background: var(--background);
      color: var(--on-surface-variant);
      font-family: var(--font-label);
      font-size: var(--label-size);
      align-items: center;
      justify-content: center;
    }

    .gov-chip {
      min-height: 44px;
      padding: var(--space-xs) var(--space-sm);
      border-radius: var(--rounded);
      border: 1px solid var(--outline-variant);
      background: var(--surface-container);
      color: var(--on-surface);
      font-family: var(--font-label);
      font-size: var(--label-size);
      font-weight: 600;
      cursor: pointer;
      display: inline-flex;
      align-items: center;
      transition: background-color var(--dur-micro) var(--ease-out), color var(--dur-micro) var(--ease-out);
    }

    .gov-chip:hover {
      background: var(--surface-container-high);
    }

    .gov-chip.selected {
      background: var(--primary);
      color: var(--on-primary);
      border-color: var(--primary);
    }

    /* Timeline strip */
    .timeline-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(min(210px, 100%), 1fr));
      gap: var(--space-md);
    }

    .timeline-step {
      border-top: 3px solid var(--data-1);
      padding-top: var(--space-sm);
    }

    .timeline-step.step-gold {
      border-top-color: var(--data-2);
    }

    .timeline-date {
      font-family: var(--font-label);
      font-size: var(--label-size);
      font-weight: 600;
      color: var(--tertiary);
      text-transform: uppercase;
      letter-spacing: 0.06em;
    }

    /* Interactive Checklist & Watchlist */
    .checklist-item {
      display: flex;
      align-items: flex-start;
      gap: var(--space-sm);
      padding: var(--space-sm) 0;
      border-bottom: 1px solid var(--outline-variant);
    }

    .checklist-item:last-child {
      border-bottom: none;
    }

    .check-btn {
      min-width: 44px;
      min-height: 44px;
      border-radius: var(--rounded);
      border: 1px solid var(--btn-secondary-border);
      background: var(--surface-container-low);
      color: var(--on-surface);
      cursor: pointer;
      display: inline-flex;
      align-items: center;
      justify-content: center;
      flex-shrink: 0;
    }

    .check-btn[aria-checked="true"] {
      background: var(--positive);
      border-color: var(--positive);
      color: var(--on-error);
    }

    .checklist-text.done {
      text-decoration: line-through;
      color: var(--on-surface-variant);
    }

    .progress-track {
      width: 100%;
      height: 8px;
      background: var(--surface-container-high);
      border-radius: var(--rounded);
      overflow: hidden;
      margin: var(--space-sm) 0;
    }

    .progress-fill {
      height: 100%;
      background: var(--positive);
      width: 0%;
      transition: width var(--dur-short) var(--ease-out);
    }

    /* Card container queries */
    .card-wrapper { container-type: inline-size; }
    @container (min-width: 460px) {
      .persona-inner { grid-template-columns: repeat(3, minmax(0, 1fr)); }
    }
    @container (max-width: 459px) {
      .persona-inner { grid-template-columns: minmax(0, 1fr); }
    }

    .persona-inner {
      display: grid;
      gap: var(--space-md);
    }

    .status-banner {
      font-family: var(--font-label);
      font-size: var(--label-size);
      color: var(--positive);
      min-height: 1.25rem;
    }

    .divider-list {
      list-style: none;
      padding: 0;
      margin: 0;
    }

    .divider-list li {
      padding: var(--space-sm) 0;
      border-bottom: 1px solid var(--outline-variant);
    }

    .divider-list li:last-child {
      border-bottom: none;
    }

    @media (max-width: 960px) {
      .header-grid,
      .split-layout,
      .equal-grid-2 {
        grid-template-columns: minmax(0, 1fr);
      }
      .matrix-grid {
        grid-template-columns: minmax(0, 1fr);
      }
    }

    @media (max-width: 640px) {
      .page-grid {
        padding: var(--space-md);
        gap: var(--space-lg);
      }
      .figure-card {
        padding: var(--space-md);
      }
    }

    @media (prefers-reduced-motion: reduce) {
      *, *::before, *::after {
        animation-duration: 0.01ms !important;
        transition-duration: 0.01ms !important;
        scroll-behavior: auto !important;
      }
    }
  </style>
</head>
<body>
  <div class="page-grid">

    <!-- HEADER & TOP DOSSIER BAR -->
    <header>
      <div class="dossier-bar">
        <div class="dossier-bar-left">
          <span class="dossier-pill">SLED Market Dossier</span>
          <span class="dossier-meta">Post-Election Briefing · Unofficial Results as of Nov. 10, 2026</span>
        </div>
        <span class="dossier-meta">Briefing Date: Nov. 18, 2026</span>
      </div>

      <div class="header-grid">
        <div>
          <h1 class="title">2026 Post-Election SLED Market Intelligence</h1>
          <p class="standfirst">The biggest shift in state power since 2008 meets $160.2B in state and local IT demand—navigate governor transitions, federal funding timelines, AI and data-center signals, and 2027 seller strategy.</p>
        </div>
        <div class="header-status-box">
          <span class="index-num">STILL UNCALLED (AS OF NOV. 10)</span>
          <div style="font-size: 0.875rem; line-height: 1.45;">
            <strong>Senate:</strong> MS, AK (D leading both) · <strong>House:</strong> 20 seats; LA runoffs Dec. 12<br>
            <strong>Governors:</strong> KS (cert. Nov. 15), AK (RCV Nov. 18) · <strong>Legislatures:</strong> NC Senate, AK chambers; GA House runoff Dec. 1
          </div>
        </div>
      </div>

      <nav class="jump-nav" aria-label="Dashboard sections">
        <a class="jump-link" href="#sec-electoral"><span class="index-num">01</span> Electoral Shift</a>
        <a class="jump-link" href="#sec-governors"><span class="index-num">02</span> Governors &amp; Transitions</a>
        <a class="jump-link" href="#sec-funding"><span class="index-num">03</span> Federal Funding</a>
        <a class="jump-link" href="#sec-tech"><span class="index-num">04</span> AI, Data Centers &amp; Demand</a>
        <a class="jump-link" href="#sec-playbook"><span class="index-num">05</span> Seller Playbook &amp; Tracker</a>
      </nav>
    </header>

    <!-- HEADLINE KPI STRIP -->
    <section aria-label="Headline SLED Market Metrics">
      <div class="kpi-grid">
        <article class="figure-card kpi-card kpi-gold">
          <div class="kpi-label">2026 State &amp; Local IT Spend</div>
          <div class="value kpi-value">$160.2B</div>
          <div class="kpi-delta">+4–6% YoY growth</div>
          <div class="kpi-detail">Spending keeps climbing even as overall state budgets stayed flat.</div>
        </article>

        <article class="figure-card kpi-card">
          <div class="kpi-label">Democratic Governorships</div>
          <div class="value kpi-value">28+</div>
          <div class="kpi-delta">5–0 flips (GA, IA, NV, OH, TX)</div>
          <div class="kpi-detail">First majority in 16 years; Democrats govern states home to ~3/4 of Americans.</div>
        </article>

        <article class="figure-card kpi-card">
          <div class="kpi-label">New Governors in Jan. 2027</div>
          <div class="value kpi-value">19–21</div>
          <div class="kpi-delta">69% CIO turnover after party flip</div>
          <div class="kpi-detail">19 confirmed, 21 if AK &amp; KS hold; 60% CIO turnover after same-party succession.</div>
        </article>

        <article class="figure-card kpi-card">
          <div class="kpi-label">Legislatures &amp; AGs</div>
          <div class="value kpi-value">~12 / 29</div>
          <div class="kpi-delta">6 new D trifectas · 5 AG flips</div>
          <div class="kpi-detail">Most flipped chambers since 2008; new trifectas in AZ, MI, MN, NV, PA, WI; 29 D AGs.</div>
        </article>

        <article class="figure-card kpi-card">
          <div class="kpi-label">State AI Laws Since 2025</div>
          <div class="value kpi-value">268</div>
          <div class="kpi-delta">159 in 2025 + 109 in H1 2026</div>
          <div class="kpi-detail">98% of states hold AI policies; federal preemption stalled in Senate.</div>
        </article>
      </div>
    </section>

    <!-- SECTION 01: ELECTORAL LANDSCAPE & STATE MAP -->
    <section id="sec-electoral" aria-labelledby="heading-01">
      <div class="dossier-bar">
        <div class="dossier-bar-left">
          <span class="dossier-pill">01 · Electoral Shift</span>
          <span class="dossier-meta">Power Map, Federal Chambers &amp; Historic Records</span>
        </div>
        <span class="dossier-meta">49.1% Turnout · ~13 pt House Margin</span>
      </div>

      <h2 id="heading-01" class="section-heading">State Power &amp; Legislative Control Explorer</h2>
      <p class="section-deck">Filter the 31 states highlighted in post-election SLED market tracking and select any state on the map or directory strip to inspect its governor outcome, legislative balance, CIO transition risk, and policy signals.</p>

      <div class="filter-row" role="group" aria-label="Filter states by electoral category">
        <button type="button" class="btn btn-secondary filter-chip" data-map-filter="all" aria-pressed="true" onclick="setMapFilter('all')">All Highlighted (31)</button>
        <button type="button" class="btn btn-secondary filter-chip" data-map-filter="flip" aria-pressed="false" onclick="setMapFilter('flip')">Flipped R→D Gov (5)</button>
        <button type="button" class="btn btn-secondary filter-chip" data-map-filter="trifecta" aria-pressed="false" onclick="setMapFilter('trifecta')">New D Trifectas (6)</button>
        <button type="button" class="btn btn-secondary filter-chip" data-map-filter="continuity" aria-pressed="false" onclick="setMapFilter('continuity')">Continuity Markets (6)</button>
        <button type="button" class="btn btn-secondary filter-chip" data-map-filter="divided" aria-pressed="false" onclick="setMapFilter('divided')">Divided Govt (6)</button>
        <button type="button" class="btn btn-secondary filter-chip" data-map-filter="uncalled" aria-pressed="false" onclick="setMapFilter('uncalled')">Uncalled / Runoff (4)</button>
      </div>

      <div class="split-layout">
        <div class="figure-card">
          <div style="display: flex; justify-content: space-between; align-items: baseline; flex-wrap: wrap; gap: var(--space-sm);">
            <h3 class="subhead">2026 Post-Election State Market Map</h3>
            <span class="dossier-meta">Click any highlighted state to inspect</span>
          </div>
          <div id="us-state-map" class="map-container" aria-label="United States post-election SLED state classification map"></div>
          <div class="legend-row" aria-label="Map classification legend">
            <span class="legend-item"><span class="legend-swatch" style="background: var(--data-2);"></span> Flipped R→D Governor (5)</span>
            <span class="legend-item"><span class="legend-swatch" style="background: var(--data-1);"></span> New D Trifecta / Continuity (10)</span>
            <span class="legend-item"><span class="legend-swatch" style="background: var(--data-3);"></span> Other Incoming Gov / Key Signal (12)</span>
            <span class="legend-item"><span class="legend-swatch" style="background: var(--data-4);"></span> Uncalled / Late Count (4)</span>
            <span class="legend-item"><span class="legend-swatch" style="background: var(--map-neutral);"></span> Not detailed in briefing</span>
          </div>
          <div id="state-pill-strip" class="state-pill-grid" aria-label="Quick state selector"></div>
          <p class="source-note">Boundaries: Natural Earth / Plotly USA States · Data: NBC News, NCSL, MultiState, DLCC, DAGA, CAWP (unofficial as of Nov. 10, 2026).</p>
        </div>

        <aside class="figure-card" aria-live="polite" id="state-inspector-card">
          <div class="dossier-bar" style="margin-bottom: var(--space-sm);">
            <span class="index-num" id="inspector-kicker">STATE DOSSIER</span>
            <span class="tag tag-slate" id="inspector-category-tag">Flipped R→D</span>
          </div>
          <h3 class="section-heading" id="inspector-state-name">Ohio (OH)</h3>
          <p id="inspector-summary" style="margin-top: 0;">First woman governor elected (Amy Acton, D); flipped from Republican to Democrat in a newly divided government.</p>
          <ul class="divider-list" id="inspector-details"></ul>
          <div class="aside-box" style="margin-top: var(--space-md);">
            <span class="index-num">SELLER BRIEFING NOTE</span>
            <p id="inspector-seller-note" style="margin: var(--space-xs) 0 0 0; font-size: 0.9rem;"></p>
          </div>
        </aside>
      </div>

      <div class="equal-grid-2" style="margin-top: var(--space-lg);">
        <div class="figure-card">
          <h3 class="subhead">Historic Records &amp; Women in Office (2027)</h3>
          <p class="source-note" style="margin-top: var(--space-xs); margin-bottom: var(--space-sm);">Record representation across governorships, House, and statewide executives; Senate women fell from 27 to at least 23.</p>
          <div class="chart-container">
            <canvas id="chart-representation" aria-label="Bar chart of women's representation and turnout benchmarks"></canvas>
          </div>
          <ul class="divider-list" style="margin-top: var(--space-sm); font-size: 0.875rem;">
            <li><strong>Firsts at Governor:</strong> Keisha Lance Bottoms (D-GA, first Black woman governor in U.S.); Deb Haaland (D-NM, first Native American woman, all-women-of-color ticket); Gina Hinojosa (D-TX, first D since 1990); Acton (OH), Klobuchar (MN), Blackburn (TN) first women in their states; Crowley (WI), Donalds (FL), Ford (NV) first Black governors of their states.</li>
            <li><strong>Firsts in Senate:</strong> Peggy Flanagan (D-MN, first Native American woman); Abdul El-Sayed (D-MI, first Muslim); Alsobrooks, Blunt Rochester, Stratton (record 3 Black women serving together).</li>
          </ul>
          <p class="source-note">Sources: Center for American Women and Politics (CAWP), Rutgers (Nov. 9, 2026); CFR; NPR; ABC News.</p>
        </div>

        <div class="figure-card">
          <h3 class="subhead">Federal &amp; State Scoreboard + CIO Turnover Risk</h3>
          <div class="table-wrap" style="margin-top: var(--space-sm);">
            <table class="briefing-table">
              <thead>
                <tr>
                  <th>Arena</th>
                  <th>Result (Unofficial, Nov. 10)</th>
                </tr>
              </thead>
              <tbody>
                <tr>
                  <td><strong>U.S. Senate</strong></td>
                  <td>Democratic control (at least 55 D). MS and AK uncalled; D leading both.</td>
                </tr>
                <tr>
                  <td><strong>U.S. House</strong></td>
                  <td>Democrats won 254 seats (~13 pt margin); 20 uncalled incl. 2 LA runoffs Dec. 12.</td>
                </tr>
                <tr>
                  <td><strong>Governors</strong></td>
                  <td>5 flips to D (GA, IA, OH, TX, NV); D leads AK &amp; KS; 19 new governors (21 if both hold).</td>
                </tr>
                <tr>
                  <td><strong>Legislatures</strong></td>
                  <td>~12 chambers flipped to D; new D trifectas in AZ (first since 1966), MI, MN, NV, PA, WI. Flipped houses: TX (since 2002), GA (91–86, runoff Dec. 1), NC.</td>
                </tr>
                <tr>
                  <td><strong>Attorneys General</strong></td>
                  <td>Democrats claim 29 offices after 5 flips (GA, IA, KS, OH, TX per DAGA).</td>
                </tr>
              </tbody>
            </table>
          </div>
          <div style="margin-top: var(--space-md);">
            <span class="index-num">CIO TURNOVER WITHIN 1 YEAR OF TRANSITION</span>
            <div class="chart-container" style="min-height: 155px; margin-top: var(--space-xs);">
              <canvas id="chart-cio-turnover" aria-label="Horizontal bar chart comparing CIO turnover rate after party flip versus same-party succession"></canvas>
            </div>
          </div>
          <p class="source-note">Sources: Yahoo News/AP, DAGA, NCSL, GovTech (2022 CIO analysis).</p>
        </div>
      </div>
    </section>

    <!-- SECTION 02: 21 INCOMING GOVERNORS, TRANSITIONS & BALLOT MEASURES -->
    <section id="sec-governors" aria-labelledby="heading-02">
      <div class="dossier-bar">
        <div class="dossier-bar-left">
          <span class="dossier-pill">02 · Governors &amp; Transitions</span>
          <span class="dossier-meta">Tech Stances of 21 Campaigns, Transition Leads &amp; Ballot Measures</span>
        </div>
        <span class="dossier-meta">Inaugurations: OH Jan. 11 · IA Jan. 12 · TX Jan. 19</span>
      </div>

      <h2 id="heading-02" class="section-heading">Where the 21 New Governors Stand &amp; Who Runs Transitions</h2>
      <p class="section-deck">Explore the 21 incoming governors by their data-center and AI campaign positions, then filter the transition directory, statewide enforcers, and revenue ballot measures.</p>

      <div class="split-layout">
        <div class="figure-card">
          <div style="display: flex; justify-content: space-between; align-items: baseline; flex-wrap: wrap; gap: var(--space-sm);">
            <h3 class="subhead">Campaign Matrix: Data Centers × AI Posture (21 Incoming Governors)</h3>
            <button type="button" class="btn btn-secondary" onclick="clearGovernorSelection()">Reset Selection</button>
          </div>
          <p class="source-note" style="margin-top: var(--space-xs);">Select any state chip in the matrix to view its strategic persona ("Builders", "Guardians", or "Policy-first") and pitch angle.</p>

          <div class="matrix-grid" role="region" aria-label="21 incoming governors stance matrix">
            <div class="matrix-head">Data Centers ↓ · AI →</div>
            <div class="matrix-head">Wants AI in Govt (3)</div>
            <div class="matrix-head">Leads with Guardrails (4)</div>
            <div class="matrix-head">Little Said on AI (14)</div>

            <div class="matrix-row-label">
              <span>Pause them</span>
              <span class="tag tag-brick">8 States</span>
            </div>
            <div class="matrix-cell empty-cell">—</div>
            <div class="matrix-cell" id="cell-pause-guard"></div>
            <div class="matrix-cell" id="cell-pause-little"></div>

            <div class="matrix-row-label">
              <span>Make them pay own way</span>
              <span class="tag tag-warm">10 States</span>
            </div>
            <div class="matrix-cell" id="cell-pay-wants"></div>
            <div class="matrix-cell" id="cell-pay-guard"></div>
            <div class="matrix-cell" id="cell-pay-little"></div>

            <div class="matrix-row-label">
              <span>Welcome them</span>
              <span class="tag tag-pine">1 State</span>
            </div>
            <div class="matrix-cell empty-cell">—</div>
            <div class="matrix-cell empty-cell">—</div>
            <div class="matrix-cell" id="cell-welcome-little"></div>

            <div class="matrix-row-label">
              <span>No stated position</span>
              <span class="tag tag-slate">2 States</span>
            </div>
            <div class="matrix-cell empty-cell">—</div>
            <div class="matrix-cell empty-cell">—</div>
            <div class="matrix-cell" id="cell-none-little"></div>
          </div>
        </div>

        <aside class="figure-card card-wrapper" aria-live="polite">
          <span class="index-num">SELECTED GOVERNOR PLAYBOOK</span>
          <h3 class="section-heading" id="gov-persona-title" style="margin-top: var(--space-xs);">Three Seller Archetypes</h3>
          <p id="gov-persona-desc" style="margin-top: 0;">Select a state chip on the left or review the three tiers below. Note: Minnesota's Amy Klobuchar is the only incoming governor explicitly pledging to replace legacy benefits systems ("they still have cursors and blinkers").</p>

          <div class="persona-inner" style="margin-top: var(--space-md);">
            <div class="aside-box" id="tier-builders" style="padding: var(--space-sm) var(--space-md);">
              <span class="tag tag-pine">Builders</span>
              <div style="font-weight: 600; margin-top: var(--space-xs);">CA · CO · WI</div>
              <p style="font-size: 0.85rem; margin: var(--space-xs) 0 0 0;">Bring named use cases (CA permitting/benefits; CO public safety/water; WI efficiency) with audits, human review, and worker input.</p>
            </div>
            <div class="aside-box" id="tier-guardians" style="padding: var(--space-sm) var(--space-md);">
              <span class="tag tag-slate">Guardians</span>
              <div style="font-weight: 600; margin-top: var(--space-xs);">AK · IA · MI · TN</div>
              <p style="font-size: 0.85rem; margin: var(--space-xs) 0 0 0;">Lead with child safety, privacy, and disclosure from day one before discussing automation.</p>
            </div>
            <div class="aside-box" id="tier-policy" style="padding: var(--space-sm) var(--space-md);">
              <span class="tag tag-warm">Policy-First</span>
              <div style="font-size: 0.85rem; font-weight: 600; margin-top: var(--space-xs);">14 Other Incoming Govs</div>
              <p style="font-size: 0.85rem; margin: var(--space-xs) 0 0 0;">Sell directly to their top goals: Medicaid, fraud reduction, schools, and household costs.</p>
            </div>
          </div>
          <p class="source-note">Source: Campaign sites, platforms, and interviews through Nov. 2026.</p>
        </aside>
      </div>

      <!-- Transition Contacts Table & Ballot Measures / Statewide Offices -->
      <div class="figure-card" style="margin-top: var(--space-lg);">
        <div class="filter-row" style="justify-content: space-between; margin-bottom: var(--space-sm);">
          <div>
            <h3 class="subhead">Who to Brief Now: Transition Leads &amp; Current State CIOs</h3>
            <p class="source-note" style="margin-top: var(--space-xs);">Be prepared to brief teams on what you have in flight, what it saves, and what pausing it would cost.</p>
          </div>
          <div style="display: flex; flex-wrap: wrap; gap: var(--space-sm); align-items: end;">
            <div class="field-group">
              <label class="field-label" for="transition-search">Search State, Lead, or CIO</label>
              <input id="transition-search" class="input-control" type="text" placeholder="Filter transitions..." oninput="filterTransitions()">
            </div>
            <div class="field-group">
              <label class="field-label" for="transition-type">Control Filter</label>
              <select id="transition-type" class="select-control" onchange="filterTransitions()">
                <option value="all">All Transitions (7)</option>
                <option value="flip">Flipped R→D (5)</option>
                <option value="same">Same-Party Succession (2)</option>
              </select>
            </div>
          </div>
        </div>

        <div class="table-wrap">
          <table class="briefing-table" id="transition-table">
            <thead>
              <tr>
                <th><button type="button" class="sort-btn" onclick="sortTransitions('state')">State ↕</button></th>
                <th><button type="button" class="sort-btn" onclick="sortTransitions('lead')">Transition Lead ↕</button></th>
                <th><button type="button" class="sort-btn" onclick="sortTransitions('cio')">State CIO Today ↕</button></th>
                <th>Status &amp; Inauguration</th>
                <th>Briefing / Input Channel</th>
              </tr>
            </thead>
            <tbody id="transition-tbody"></tbody>
          </table>
        </div>
        <p class="source-note">As of Nov. 10, 2026 · Sources: KUT, FOX 5 Atlanta, Morning Journal, CBS2 Iowa, Yellow Scene, WSMV, GovTech.</p>
      </div>

      <div class="equal-grid-2" style="margin-top: var(--space-lg);">
        <div class="figure-card">
          <h3 class="subhead">Ballot Measures: Revenue Winners &amp; Losers</h3>
          <div class="table-wrap" style="margin-top: var(--space-sm);">
            <table class="briefing-table">
              <thead>
                <tr>
                  <th>Measure</th>
                  <th>Result</th>
                  <th>Market Impact</th>
                </tr>
              </thead>
              <tbody>
                <tr>
                  <td><strong>FL Amendment 3</strong> (homestead exemption)</td>
                  <td><span class="tag tag-pine">Passed 62%</span></td>
                  <td>Counties and cities lose revenue; schools exempt.</td>
                </tr>
                <tr>
                  <td><strong>NC</strong> levy-growth &amp; income-tax caps</td>
                  <td><span class="tag tag-pine">Passed</span></td>
                  <td>Local government revenue becomes less elastic.</td>
                </tr>
                <tr>
                  <td><strong>IA</strong> 2/3 vote to raise income tax</td>
                  <td><span class="tag tag-pine">Passed</span></td>
                  <td>Limits the incoming governor's fiscal options.</td>
                </tr>
                <tr>
                  <td><strong>CO Prop NN</strong> (TABOR surplus to K-12)</td>
                  <td><span class="tag tag-pine">Passed 55%</span></td>
                  <td>$330M–$500M in year one for K-12 education.</td>
                </tr>
                <tr>
                  <td><strong>CA Prop 3</strong> (high-income tax for schools)</td>
                  <td><span class="tag tag-slate">Leading 59–41</span></td>
                  <td>Removes a K-12 and community college funding cliff.</td>
                </tr>
                <tr>
                  <td><strong>CO Amend. 87, WA I-645, AK Measure 2</strong></td>
                  <td><span class="tag tag-warm">Too close</span></td>
                  <td>Watch late counts for fiscal impact.</td>
                </tr>
              </tbody>
            </table>
          </div>
          <div class="aside-box" style="margin-top: var(--space-sm);">
            <strong>Key takeaway:</strong> No statewide AI, privacy, or broadband measure was on any ballot. Technology policy stays with state legislatures.
          </div>
        </div>

        <div class="figure-card">
          <h3 class="subhead">Statewide Offices: Enforcers &amp; Education Chiefs</h3>
          <div class="card-grid" style="margin-top: var(--space-sm); grid-template-columns: minmax(0, 1fr);">
            <div class="aside-box">
              <span class="index-num">ATTORNEYS GENERAL (29 D OFFICES)</span>
              <p style="margin: var(--space-xs) 0 0 0; font-size: 0.9rem;">Democrats claim 29 offices after 5 flips (GA, IA, KS, OH, TX). Child online safety is a bipartisan priority; Texas's AI law is AG-enforced.</p>
            </div>
            <div class="aside-box">
              <span class="index-num">STATE SCHOOL CHIEFS</span>
              <p style="margin: var(--space-xs) 0 0 0; font-size: 0.9rem;">Georgia: Powell (D) ousted incumbent Woods · Arizona: Leyba Ruiz (D) won · Oklahoma: Franklin won · California: Barrera won.</p>
            </div>
            <div class="aside-box">
              <span class="index-num">SECRETARIES OF STATE</span>
              <p style="margin: var(--space-xs) 0 0 0; font-size: 0.9rem;">Georgia and Indiana flip to D; Arizona and California incumbents re-elected. Election-tech vendors should expect contract and security reviews in new offices.</p>
            </div>
          </div>
          <p class="source-note">So what? Expect heightened privacy, AI, and student-data enforcement plus contract reviews at state education agencies. Lead with data governance.</p>
        </div>
      </div>
    </section>

    <!-- SECTION 03: FEDERAL FUNDING STREAMS -->
    <section id="sec-funding" aria-labelledby="heading-03">
      <div class="dossier-bar">
        <div class="dossier-bar-left">
          <span class="dossier-pill">03 · Federal Funding</span>
          <span class="dossier-meta">FY2027–2028 Calendar, BEAD, E-Rate &amp; H.R. 1 Mandates</span>
        </div>
        <span class="dossier-meta">Lead with State Budgets, Formula Dollars &amp; Matches</span>
      </div>

      <h2 id="heading-03" class="section-heading">Federal Funding Streams &amp; 2027 Pipeline</h2>
      <p class="section-deck">Plan for steady but slower federal awards (roughly one quarter late). Braid grants, use cooperative contracts, and anchor pitches to mandated H.R. 1 and broadband work.</p>

      <div class="figure-card" style="margin-bottom: var(--space-lg);">
        <h3 class="subhead">Federal Budget Calendar for Your 2027 Pipeline</h3>
        <div class="timeline-grid" style="margin-top: var(--space-md);">
          <div class="timeline-step step-gold">
            <div class="timeline-date">Dec. 11, 2026</div>
            <div style="font-weight: 600; margin: var(--space-xs) 0;">Current Stopgap Ends</div>
            <div style="font-size: 0.875rem; color: var(--on-surface-variant);">Another short-term continuing extension is the likely path.</div>
          </div>
          <div class="timeline-step">
            <div class="timeline-date">Jan. 3, 2027</div>
            <div style="font-weight: 600; margin: var(--space-xs) 0;">New Congress Convenes</div>
            <div style="font-size: 0.875rem; color: var(--on-surface-variant);">Opens with likely Democratic majorities in House and Senate.</div>
          </div>
          <div class="timeline-step">
            <div class="timeline-date">Spring 2027</div>
            <div style="font-weight: 600; margin: var(--space-xs) 0;">Full-Year FY2027 Settled</div>
            <div style="font-size: 0.875rem; color: var(--on-surface-variant);">Funding likely settles near prior levels; expect awards ~1 quarter late.</div>
          </div>
          <div class="timeline-step">
            <div class="timeline-date">FY2028</div>
            <div style="font-weight: 600; margin: var(--space-xs) 0;">New Congress Budget</div>
            <div style="font-size: 0.875rem; color: var(--on-surface-variant);">First federal budget authored entirely by the new Congress.</div>
          </div>
        </div>
      </div>

      <div class="filter-row" role="group" aria-label="Select federal funding stream">
        <button type="button" class="btn btn-secondary filter-chip" data-stream="all" aria-pressed="true" onclick="setFundingStream('all')">All 3 Streams Overview</button>
        <button type="button" class="btn btn-secondary filter-chip" data-stream="bead" aria-pressed="false" onclick="setFundingStream('bead')">BEAD ($42.45B Broadband)</button>
        <button type="button" class="btn btn-secondary filter-chip" data-stream="erate" aria-pressed="false" onclick="setFundingStream('erate')">E-Rate (School Networks)</button>
        <button type="button" class="btn btn-secondary filter-chip" data-stream="hr1" aria-pressed="false" onclick="setFundingStream('hr1')">H.R. 1 (Medicaid &amp; SNAP)</button>
      </div>

      <div class="card-grid" id="funding-cards-grid">
        <article class="figure-card funding-panel" data-stream-card="bead">
          <div class="dossier-bar" style="margin-bottom: var(--space-sm);">
            <span class="index-num">BROADBAND</span>
            <span class="tag tag-pine">Bipartisan</span>
          </div>
          <div class="value" style="font-size: 2rem;">$42.45B BEAD</div>
          <p style="font-size: 0.9rem; margin: var(--space-xs) 0;">Created by the 2021 infrastructure law (Senate 69–30 with 19 Republicans); rewritten in 2025 ("Benefit of the Bargain"), not repealed. All 56 state and territory final proposals are now approved.</p>
          <ul class="divider-list" style="font-size: 0.875rem;">
            <li><strong>Remaining funds:</strong> ~$21B left over after lower-cost awards; NTIA says it can fund additional deployment through 2030.</li>
            <li><strong>Where sellers fit:</strong> Network gear &amp; services to winning ISPs; grant management, compliance, and mapping tools for state broadband offices.</li>
            <li><strong>Watch:</strong> Fights are over rules, not survival. A 2025 executive order ties some leftover BEAD money to state AI laws.</li>
          </ul>
          <p class="source-note">Sources: NTIA, Telecompetitor, Next TV, Broadband Breakfast, Tech Policy Press.</p>
        </article>

        <article class="figure-card funding-panel" data-stream-card="erate">
          <div class="dossier-bar" style="margin-bottom: var(--space-sm);">
            <span class="index-num">K-12 &amp; LIBRARIES</span>
            <span class="tag tag-slate">Upheld 6–3 SCOTUS</span>
          </div>
          <div class="value" style="font-size: 2rem;">E-Rate (1996–)</div>
          <p style="font-size: 0.9rem; margin: var(--space-xs) 0;">Bipartisan since 1996 and upheld 6–3 by the Supreme Court in 2025. E-Rate now pays to maintain school networks and internal connectivity—not devices or instructional apps.</p>
          <ul class="divider-list" style="font-size: 0.875rem;">
            <li><strong>Policy shift:</strong> New 2026 laws in Iowa, Tennessee, and Utah limit student device screen time in early grades.</li>
            <li><strong>How to stand out:</strong> Demonstrate how your solution protects students and student data while measurably improving learning outcomes.</li>
          </ul>
          <p class="source-note">Sources: Center for Digital Education analysis of USAC requests (GovTech, Oct. 2026); Whiteboard Advisors.</p>
        </article>

        <article class="figure-card funding-panel" data-stream-card="hr1">
          <div class="dossier-bar" style="margin-bottom: var(--space-sm);">
            <span class="index-num">MANDATED SYSTEMS</span>
            <span class="tag tag-gold">2027 Deadlines</span>
          </div>
          <div class="value" style="font-size: 2rem;">H.R. 1 Eligibility</div>
          <p style="font-size: 0.9rem; margin: var(--space-xs) 0;">Hands states a strict Medicaid and SNAP modernization to-do list regardless of who holds the governor's office.</p>
          <ul class="divider-list" style="font-size: 0.875rem;">
            <li><strong>Oct. 2026:</strong> States pay 75% of SNAP admin costs (up from 50%).</li>
            <li><strong>Jan. 1, 2027:</strong> Medicaid work requirements (80 hrs/month for expansion adults).</li>
            <li><strong>2027 &amp; Oct. 2027:</strong> Six-month Medicaid renewals; SNAP benefit costs shift to states with high error rates.</li>
            <li><strong>Funding:</strong> 90% federal match to build Medicaid systems, 75% to run them; $200M implementation grants; $50B Rural Health Transformation Program (technology is 1 of 5 goals).</li>
            <li><strong>Where sellers fit:</strong> Income/work verification, ex parte renewals, caseworker &amp; outreach tools, error-rate reduction.</li>
          </ul>
          <p class="source-note">Sources: CMS, HMA (Nov. 2026), CBPP, FRAC, Healthcare Dive. Dates may shift with CMS rules/courts.</p>
        </article>
      </div>
    </section>

    <!-- SECTION 04: TECH SIGNALS, DATA CENTERS & DEMAND POOLS -->
    <section id="sec-tech" aria-labelledby="heading-04">
      <div class="dossier-bar">
        <div class="dossier-bar-left">
          <span class="dossier-pill">04 · Tech Signals &amp; Demand</span>
          <span class="dossier-meta">Data Center Backlash, 268 AI Laws &amp; Cross-Party Pools</span>
        </div>
        <span class="dossier-meta">Trust Is Now Part of the Sale</span>
      </div>

      <h2 id="heading-04" class="section-heading">Data Centers, State AI Laws &amp; Four Cross-Party Demand Pools</h2>
      <p class="section-deck">Voters rejected 46 of 47 data-center measures while states enacted 268 AI laws since 2025. Adjust sales messaging to governance and resident outcomes—not product rebuilds.</p>

      <div class="equal-grid-2">
        <div class="figure-card">
          <h3 class="subhead">Data Center Voter Backlash (2026)</h3>
          <div class="kpi-grid" style="margin: var(--space-sm) 0;">
            <div class="aside-box" style="padding: var(--space-sm) var(--space-md);">
              <div class="value" style="font-size: 1.75rem; color: var(--error);">46 of 47</div>
              <div style="font-size: 0.85rem;">Decided ballot measures favored limiting data centers.</div>
            </div>
            <div class="aside-box" style="padding: var(--space-sm) var(--space-md);">
              <div class="value" style="font-size: 1.75rem;">56% / 61%</div>
              <div style="font-size: 0.85rem;">56% cited AI/data centers as key vote factor; 61% oppose one nearby (+12 pts in 4 mos).</div>
            </div>
          </div>
          <ul class="divider-list" style="font-size: 0.875rem;">
            <li><strong>Political risk is real:</strong> 28 state data-center laws enacted by July 2026; Piqua and Urbana, Ohio recalled local officials over data-center deals.</li>
            <li><strong>Transparency rules:</strong> Disclose power, water, and tax deals up front; drop NDAs with local officials; publish jobs and revenue after opening.</li>
            <li><strong>Community license:</strong> Engage residents before rezoning, expect local votes on large projects, bring own power/water, and hire locally.</li>
          </ul>
          <p class="source-note">Sources: Ballotpedia (Nov. 10), NBC Exit Poll via Route Fifty, Annenberg Public Policy Center (Aug. 2026), WOSU, Tech Policy Press.</p>
        </div>

        <div class="figure-card">
          <h3 class="subhead">State AI Law Patchwork &amp; Enterprise Readiness</h3>
          <p class="source-note" style="margin-top: var(--space-xs); margin-bottom: var(--space-sm);">Federal preemption stalled (Senate rejected moratorium in 2025; a Democratic Congress won't revive it, though DOJ's task force can still sue). Build to the common core: disclosure, human review, bias testing.</p>
          <div class="chart-container">
            <canvas id="chart-ai-laws" aria-label="Chart of state AI laws enacted in 2025 and H1 2026 alongside NASCIO state AI policy adoption"></canvas>
          </div>
          <p class="source-note">Sources: NYU Center on Technology Policy via Tech Policy Press (July 2026), NCSL, NASCIO 2026 CIO Survey.</p>
        </div>
      </div>

      <div style="margin-top: var(--space-lg);">
        <h3 class="subhead" style="margin-bottom: var(--space-sm);">Four Demand Pools Crossing Party Lines ($160.2B IT Market)</h3>
        <div class="card-grid">
          <article class="figure-card">
            <span class="index-num">POOL 01 · CYBERSECURITY</span>
            <h4 class="subhead" style="margin-top: var(--space-xs);">Managed &amp; Whole-of-State</h4>
            <p style="font-size: 0.875rem; margin-bottom: 0;">Federal cyber grants shrank and MS-ISAC lost roughly 70% of members, creating acute state and local demand for managed security and whole-of-state defense.</p>
          </article>
          <article class="figure-card">
            <span class="index-num">POOL 02 · AI GOVERNANCE</span>
            <h4 class="subhead" style="margin-top: var(--space-xs);">Policies, Terms &amp; Agentic AI</h4>
            <p style="font-size: 0.875rem; margin-bottom: 0;">98% of states have AI policies, 61% have GenAI contract terms, and 64% expect agentic AI to matter most—requiring auditability and human review.</p>
          </article>
          <article class="figure-card">
            <span class="index-num">POOL 03 · ELIGIBILITY &amp; FRAUD</span>
            <h4 class="subhead" style="margin-top: var(--space-xs);">H.R. 1 Funded Upgrades</h4>
            <p style="font-size: 0.875rem; margin-bottom: 0;">Early funding is already moving (IL $55M, KY $35M, MO $17.9M); major eligibility &amp; Medicaid shifts are coming in GA, TX, and IA.</p>
          </article>
          <article class="figure-card">
            <span class="index-num">POOL 04 · EFFICIENCY &amp; TRUST</span>
            <h4 class="subhead" style="margin-top: var(--space-xs);">Audits &amp; Clean Government</h4>
            <p style="font-size: 0.875rem; margin-bottom: 0;">Medicaid DOGE-style reviews (FL, TN, AL), anti-corruption orders (OH), and fraud audits (MN, IA) reward vendors proving measurable savings.</p>
          </article>
        </div>
      </div>
    </section>

    <!-- SECTION 05: STRATEGIC PLAYBOOK & INTERACTIVE ACTION TRACKER -->
    <section id="sec-playbook" aria-labelledby="heading-05">
      <div class="dossier-bar">
        <div class="dossier-bar-left">
          <span class="dossier-pill">05 · Seller Action Plan</span>
          <span class="dossier-meta">Watch, Say, Do · 30 / 90 / 180-Day Execution Tracker</span>
        </div>
        <span class="dossier-meta">Democrats Have 2 Years Before 2028 to Show Results</span>
      </div>

      <h2 id="heading-05" class="section-heading">Messages That Resonate &amp; Execution Roadmap</h2>
      <p class="section-deck">Work that delivers visible resident results before 2028 (faster benefits, lower utility/housing/health bills, cleaner contracting) gets funded first. Filter messaging by party and track your team's transition playbook below.</p>

      <div class="equal-grid-2">
        <!-- Message Translator -->
        <div class="figure-card">
          <div style="display: flex; justify-content: space-between; align-items: baseline; flex-wrap: wrap; gap: var(--space-sm);">
            <h3 class="subhead">Say This (Outcome) vs. Not This (Tech)</h3>
          </div>
          <div class="filter-row" style="margin-top: var(--space-sm); margin-bottom: var(--space-sm);" role="group" aria-label="Filter messaging by audience">
            <button type="button" class="btn btn-secondary filter-chip" data-msg-filter="all" aria-pressed="true" onclick="setMessageFilter('all')">All / Bipartisan</button>
            <button type="button" class="btn btn-secondary filter-chip" data-msg-filter="dem" aria-pressed="false" onclick="setMessageFilter('dem')">Democrats (Affordability)</button>
            <button type="button" class="btn btn-secondary filter-chip" data-msg-filter="gop" aria-pressed="false" onclick="setMessageFilter('gop')">Republicans (Efficiency)</button>
          </div>
          <div class="table-wrap">
            <table class="briefing-table">
              <thead>
                <tr>
                  <th>Say This: Resident Outcome</th>
                  <th>Not This: Technology</th>
                </tr>
              </thead>
              <tbody id="messaging-tbody"></tbody>
            </table>
          </div>
          <div class="aside-box" style="margin-top: var(--space-sm); font-size: 0.875rem;">
            <strong>Party framing rule:</strong> Democrats emphasize <em>affordability, access, and helping people</em>; Republicans emphasize <em>efficiency, accountability, and results</em>. Both fund a measured outcome residents can feel.
          </div>
        </div>

        <!-- Six Signals to Watch -->
        <div class="figure-card">
          <h3 class="subhead">Six Signals to Watch in Every State</h3>
          <p class="source-note" style="margin-top: var(--space-xs);">Monitor these six indicators between November 2026 and Spring 2027 to spot funded priorities early.</p>
          <div class="card-grid" style="margin-top: var(--space-sm); grid-template-columns: repeat(auto-fit, minmax(min(210px, 100%), 1fr));">
            <div class="aside-box" style="padding: var(--space-sm) var(--space-md);">
              <span class="index-num">01 · WHO GETS HIRED</span>
              <div style="font-size: 0.875rem; margin-top: var(--space-xs);">Chiefs of staff, budget directors, and CIOs reveal a governor's priorities. Keep career deputies close—they often become the next CIO.</div>
            </div>
            <div class="aside-box" style="padding: var(--space-sm) var(--space-md);">
              <span class="index-num">02 · STATE OF THE STATE</span>
              <div style="font-size: 0.875rem; margin-top: var(--space-xs);">January speeches are the first public priority list. Track every program named.</div>
            </div>
            <div class="aside-box" style="padding: var(--space-sm) var(--space-md);">
              <span class="index-num">03 · FIRST BUDGETS</span>
              <div style="font-size: 0.875rem; margin-top: var(--space-xs);">FY2028 proposals (due Jan–Feb 2027) show what actually gets funded.</div>
            </div>
            <div class="aside-box" style="padding: var(--space-sm) var(--space-md);">
              <span class="index-num">04 · DAY-ONE ORDERS</span>
              <div style="font-size: 0.875rem; margin-top: var(--space-xs);">Executive orders on Medicaid, data centers, and ethics move faster than bills.</div>
            </div>
            <div class="aside-box" style="padding: var(--space-sm) var(--space-md);">
              <span class="index-num">05 · AUDITS &amp; REVIEWS</span>
              <div style="font-size: 0.875rem; margin-top: var(--space-xs);">Efficiency and fraud reviews in both parties can open or close procurement doors.</div>
            </div>
            <div class="aside-box" style="padding: var(--space-sm) var(--space-md);">
              <span class="index-num">06 · LEGISLATIVE DEALS</span>
              <div style="font-size: 0.875rem; margin-top: var(--space-xs);">In divided states (GA, OH, IA, TX, NH), budgets are negotiated—know leaders on both sides.</div>
            </div>
          </div>
        </div>
      </div>

      <!-- Interactive 30 / 90 / 180 Day Action Tracker (Persisted via notebookAppApi) -->
      <div class="figure-card" style="margin-top: var(--space-lg);">
        <div style="display: flex; flex-wrap: wrap; justify-content: space-between; align-items: center; gap: var(--space-md);">
          <div>
            <h3 class="subhead">Interactive 30 / 90 / 180-Day Seller Action Tracker</h3>
            <p class="source-note" style="margin-top: var(--space-xs);">Check off execution milestones and log priority states or accounts for your team. Saved automatically in your notebook session.</p>
          </div>
          <div style="display: flex; flex-wrap: wrap; gap: var(--space-sm);">
            <button type="button" class="btn btn-secondary" onclick="exportActionBrief()">Export Action Brief (.txt)</button>
            <button type="button" class="btn btn-secondary" id="reset-tracker-btn" onclick="resetTracker(this)">Reset Tracker</button>
          </div>
        </div>

        <div style="display: flex; align-items: center; justify-content: space-between; gap: var(--space-md); margin-top: var(--space-md);">
          <span class="dossier-meta" id="tracker-progress-label">0 of 9 actions completed (0%)</span>
          <span class="status-banner" id="tracker-status" aria-live="polite"></span>
        </div>
        <div class="progress-track" role="progressbar" aria-valuemin="0" aria-valuemax="100" aria-valuenow="0" id="tracker-progressbar">
          <div class="progress-fill" id="tracker-progress-fill"></div>
        </div>

        <div class="equal-grid-2" style="margin-top: var(--space-md);">
          <div id="checklist-container"></div>

          <div class="aside-box">
            <span class="index-num">TARGET ACCOUNT &amp; STATE WATCHLIST</span>
            <p style="font-size: 0.875rem; margin: var(--space-xs) 0 var(--space-sm) 0;">Add specific state agencies, transition leads, or FY2028 budget requests your team is tracking.</p>
            <div class="filter-row" style="margin-bottom: var(--space-sm);">
              <div class="field-group" style="flex: 1 1 200px;">
                <label class="field-label" for="custom-note-input">State / Agency Action Note</label>
                <input id="custom-note-input" class="input-control" type="text" placeholder="e.g., Brief OH transition team via govelectacton.com on Medicaid renewals">
              </div>
              <button type="button" class="btn btn-primary" onclick="addWatchlistNote()">Add Note</button>
            </div>
            <ul class="divider-list" id="watchlist-notes-list"></ul>
          </div>
        </div>
      </div>
    </section>

    <footer style="border-top: var(--section-rule); padding-top: var(--space-md); padding-bottom: var(--space-lg);">
      <div class="source-note">
        <strong>Compiled Sources:</strong> Post-Election &amp; SLED Market Briefing (unofficial results as of Nov. 10, 2026); e.Republic, NASBO, GovTech, Center for American Women and Politics (CAWP) at Rutgers, NCSL, DLCC, DAGA, NBC News, Yahoo News/AP, Stateline, Council on Foreign Relations, NTIA, CMS, HMA, CBPP, NYU Center on Technology Policy, Annenberg Public Policy Center, Ballotpedia, and NASCIO 2026 CIO Survey.
      </div>
    </footer>
  </div>

  <api-shim></api-shim>

  <script>
    // Read color tokens from :root dynamically so CSS remains the single source of truth
    function getToken(name) {
      return getComputedStyle(document.documentElement).getPropertyValue(name).trim();
    }

    // Complete 31-state dataset strictly grounded in the source material
    const STATES_DATA = [
      {
        code: `GA`, name: `Georgia`, cat: `flip`,
        tags: [`flip`, `divided`, `trifecta_no`],
        mapGroup: 1,
        govOutcome: `Flipped R→D: Keisha Lance Bottoms (first Black woman governor in U.S. history)`,
        legislature: `Divided govt; Georgia House flipped (91–86, 1 runoff Dec. 1)`,
        cioTransition: `Transition COO: Jon Keen · Current CIO: Shawnzia Thomas (GTA)`,
        techSignals: `Pause data centers · Little said on AI`,
        policySignals: `AG flipped to D; School chief Powell (D) ousted Woods; SoS flipped to D; Medicaid expansion & H.R. 1 shifts coming.`,
        sellerNote: `High transition urgency (69% historical CIO turnover after party flip). Lead with H.R. 1 eligibility modernization, data governance for Ed/SoS reviews, and resident-outcome proof.`
      },
      {
        code: `OH`, name: `Ohio`, cat: `flip`,
        tags: [`flip`, `divided`],
        mapGroup: 1,
        govOutcome: `Flipped R→D: Amy Acton (first woman governor of Ohio) · Inauguration Jan. 11`,
        legislature: `Divided govt`,
        cioTransition: `Transition chair: John Haseley · Current CIO: Katrina Flory · Public input: govelectacton.com`,
        techSignals: `Pause data centers · Little said on AI`,
        policySignals: `AG flipped to D; anti-corruption executive orders pledged; Piqua & Urbana recalled officials over data-center deals.`,
        sellerNote: `Big market with early Jan. 11 inauguration. Submit transition brief via govelectacton.com showing in-flight savings, clean contracting, and transparent community engagement.`
      },
      {
        code: `TX`, name: `Texas`, cat: `flip`,
        tags: [`flip`, `divided`],
        mapGroup: 1,
        govOutcome: `Flipped R→D: Gina Hinojosa (first Democrat elected TX governor since 1990) · Inauguration Jan. 19`,
        legislature: `Divided govt; Texas House flipped for first time since 2002`,
        cioTransition: `Transition leads: Joe Madden (exec. dir.), Ernest Bailes · Current CIO: Tony Sauerhoff (DIR)`,
        techSignals: `Pause data centers · Little said on AI`,
        policySignals: `AG flipped to D (TX AI law is AG-enforced); Medicaid expansion push & H.R. 1 shifts coming.`,
        sellerNote: `Major market with Jan. 19 inauguration and shared budget pen. Prepare DIR/transition brief on Medicaid/SNAP readiness and AG-compliant AI disclosure.`
      },
      {
        code: `IA`, name: `Iowa`, cat: `flip`,
        tags: [`flip`, `divided`],
        mapGroup: 1,
        govOutcome: `Flipped R→D · Inauguration Jan. 12`,
        legislature: `Divided govt`,
        cioTransition: `Transition CoS: Abe Rakov · Current CIO: Matt Behrens`,
        techSignals: `Make data centers pay own way · Leads with AI guardrails (Guardians tier)`,
        policySignals: `AG flipped to D; 2/3 vote to raise income tax passed (limits governor); end Medicaid privatization; fraud audits; 2026 early-grade screen-time law; H.R. 1 shifts coming.`,
        sellerNote: `Pitch AI with child safety, privacy, and human review. Highlight measurable fraud-audit savings and H.R. 1 Medicaid/SNAP readiness within tight tax-cap constraints.`
      },
      {
        code: `NV`, name: `Nevada`, cat: `flip`,
        tags: [`flip`, `trifecta`],
        mapGroup: 1,
        govOutcome: `Flipped R→D: Aaron Ford (first Black governor of Nevada)`,
        legislature: `New Democratic trifecta`,
        cioTransition: `Transition lead: Not yet announced · Current CIO: Tim Galluzi`,
        techSignals: `Make data centers pay own way · Little said on AI`,
        policySignals: `Unified control enables faster budgets and stalled IT requests to move.`,
        sellerNote: `Monitor transition announcement closely; unified D control accelerates FY2028 IT approvals if tied to lower household costs and fast resident services.`
      },
      {
        code: `AZ`, name: `Arizona`, cat: `trifecta`,
        tags: [`trifecta`, `continuity`],
        mapGroup: 2,
        govOutcome: `Continuity market: Incumbent governor returns`,
        legislature: `New Democratic trifecta (Arizona's first since 1966)`,
        cioTransition: `Incumbent leadership & priorities carry over`,
        techSignals: `Policy continuity`,
        policySignals: `School chief Leyba Ruiz (D) won; SoS incumbent re-elected. Faster budgets; previously vetoed bills and stalled IT asks can now move.`,
        sellerNote: `Revisit stalled IT proposals from the prior session and align with the new School Chief on student-data governance.`
      },
      {
        code: `MI`, name: `Michigan`, cat: `trifecta`,
        tags: [`trifecta`],
        mapGroup: 2,
        govOutcome: `New governor in a big market`,
        legislature: `New Democratic trifecta`,
        cioTransition: `Expect CIO and agency-head turnover`,
        techSignals: `Pause data centers · Leads with AI guardrails (Guardians tier)`,
        policySignals: `Abdul El-Sayed (D-MI) became first Muslim elected to U.S. Senate. Unified control enables faster budgets.`,
        sellerNote: `Lead with child safety, privacy, and disclosure guardrails; expect new CIO appointments and faster movement on unified-budget IT priorities.`
      },
      {
        code: `MN`, name: `Minnesota`, cat: `trifecta`,
        tags: [`trifecta`],
        mapGroup: 2,
        govOutcome: `New governor: Amy Klobuchar (first woman governor of Minnesota)`,
        legislature: `New Democratic trifecta`,
        cioTransition: `New governor administration`,
        techSignals: `No stated data-center position · Little said on AI`,
        policySignals: `ONLY incoming governor pledging to replace legacy benefits systems ("cursors and blinkers"); fraud audits active; Peggy Flanagan first Native American woman in U.S. Senate.`,
        sellerNote: `Prime legacy benefits modernization opportunity—pitch faster benefit delivery ("days, not weeks") paired with built-in fraud-audit controls.`
      },
      {
        code: `PA`, name: `Pennsylvania`, cat: `trifecta`,
        tags: [`trifecta`, `continuity`],
        mapGroup: 2,
        govOutcome: `Continuity market: Incumbent governor returns`,
        legislature: `New Democratic trifecta`,
        cioTransition: `Incumbent priorities carry over`,
        techSignals: `Continuity priorities with faster legislative velocity`,
        policySignals: `Unified control removes legislative bottlenecks on budgets and IT asks.`,
        sellerNote: `Accelerate existing pipeline deals and push stalled modernization requests into FY2028 budget bills.`
      },
      {
        code: `WI`, name: `Wisconsin`, cat: `trifecta`,
        tags: [`trifecta`],
        mapGroup: 2,
        govOutcome: `New governor (Crowley, first Black governor of WI) in a big market`,
        legislature: `New Democratic trifecta`,
        cioTransition: `Expect CIO and agency-head turnover`,
        techSignals: `Make data centers pay own way · Wants AI in govt for efficiency (Builders tier)`,
        policySignals: `Unified D control accelerates budget approvals.`,
        sellerNote: `Bring concrete government efficiency AI use cases backed by audits, human review, and labor/worker input.`
      },
      {
        code: `CA`, name: `California`, cat: `continuity`,
        tags: [`continuity`],
        mapGroup: 3,
        govOutcome: `New governor in a big market`,
        legislature: `Democratic control`,
        cioTransition: `Expect CIO and agency-head turnover (60% same-party benchmark)`,
        techSignals: `Make data centers pay own way · Wants AI in govt for permitting & benefits (Builders tier)`,
        policySignals: `CA Prop 3 leading 59–41 (removes K-12 & community college funding cliff); School chief Barrera won; SoS incumbent re-elected.`,
        sellerNote: `Pitch named AI use cases in permitting and benefits with human review and audits; engage K-12/community college buyers bolstered by Prop 3.`
      },
      {
        code: `FL`, name: `Florida`, cat: `continuity`,
        tags: [`continuity`],
        mapGroup: 3,
        govOutcome: `New governor: Byron Donalds (first Black governor of FL) in a big market`,
        legislature: `Republican control`,
        cioTransition: `Expect CIO and agency-head turnover`,
        techSignals: `Make data centers pay own way · Little said on AI`,
        policySignals: `FL Amendment 3 (homestead exemption) passed 62%—counties & cities lose revenue while schools are exempt; Medicaid DOGE-style efficiency reviews.`,
        sellerNote: `Target school districts (exempt from Amend. 3 cuts) and pitch measurable Medicaid DOGE-style cost savings to the incoming administration.`
      },
      {
        code: `NY`, name: `New York`, cat: `continuity`,
        tags: [`continuity`],
        mapGroup: 2,
        govOutcome: `Continuity market: Incumbent governor returns`,
        legislature: `Democratic control`,
        cioTransition: `Priorities carry over`,
        techSignals: `Continuity`,
        policySignals: `Stratton / Alsobrooks / Blunt Rochester milestones noted in Senate context; stable state IT direction.`,
        sellerNote: `Protect multi-year contracts and expand via existing statewide/cooperative vehicles.`
      },
      {
        code: `IL`, name: `Illinois`, cat: `continuity`,
        tags: [`continuity`],
        mapGroup: 2,
        govOutcome: `Continuity market: Incumbent governor returns`,
        legislature: `Democratic control`,
        cioTransition: `Priorities carry over`,
        techSignals: `H.R. 1 eligibility & fraud work already funded ($55M)`,
        policySignals: `Active $55M H.R. 1 modernization execution ahead of 2027 federal deadlines.`,
        sellerNote: `Benchmark Illinois H.R. 1 outcomes as peer-state proof for Georgia, Texas, and Iowa pitches.`
      },
      {
        code: `MD`, name: `Maryland`, cat: `continuity`,
        tags: [`continuity`],
        mapGroup: 2,
        govOutcome: `Continuity market: Incumbent governor returns`,
        legislature: `Democratic control`,
        cioTransition: `Priorities carry over`,
        techSignals: `Continuity`,
        policySignals: `Steady state IT governance and procurement cadence.`,
        sellerNote: `Maintain career-staff relationships and align with FY2028 budget requests.`
      },
      {
        code: `MA`, name: `Massachusetts`, cat: `continuity`,
        tags: [`continuity`],
        mapGroup: 2,
        govOutcome: `Continuity market: Incumbent governor returns`,
        legislature: `Democratic control`,
        cioTransition: `Priorities carry over`,
        techSignals: `Continuity`,
        policySignals: `Steady priorities across agency IT modernization.`,
        sellerNote: `Focus on resident outcomes and measurable SLA gains in renewals.`
      },
      {
        code: `CO`, name: `Colorado`, cat: `other`,
        tags: [`other`],
        mapGroup: 3,
        govOutcome: `New governor (same-party succession)`,
        legislature: `Democratic control`,
        cioTransition: `Transition CoS: Natalie Hanlon Leh · Current CIO: Sarah Tuneberg`,
        techSignals: `Make data centers pay own way · Wants AI in govt for public safety & water (Builders tier)`,
        policySignals: `Prop NN passed 55% ($330M–$500M in year 1 for K-12); Amend. 87 too close to call.`,
        sellerNote: `Pitch audited AI use cases in public safety and water, plus K-12 network/data-protection solutions funded by Prop NN.`
      },
      {
        code: `TN`, name: `Tennessee`, cat: `other`,
        tags: [`other`],
        mapGroup: 3,
        govOutcome: `New governor: Marsha Blackburn (first woman governor of TN)`,
        legislature: `Republican control`,
        cioTransition: `Transition chair: Stuart McWhorter · Current CIO: Kristin Darby`,
        techSignals: `Make data centers pay own way · Leads with AI guardrails (Guardians tier)`,
        policySignals: `2026 early-grade screen-time limit law; Medicaid DOGE-style efficiency reviews.`,
        sellerNote: `Lead with child safety, student-data privacy, and verifiable Medicaid efficiency savings.`
      },
      {
        code: `AK`, name: `Alaska`, cat: `uncalled`,
        tags: [`uncalled`],
        mapGroup: 4,
        govOutcome: `Uncalled (D leads; would be 6th R→D flip; RCV count Nov. 18 if needed)`,
        legislature: `Both Alaska chambers still uncalled`,
        cioTransition: `Pending final certification`,
        techSignals: `Pause data centers · Leads with AI guardrails (Guardians tier)`,
        policySignals: `U.S. Senate race also uncalled (D leading); Measure 2 too close to call.`,
        sellerNote: `Watch Nov. 18 RCV tally; prepare Guardians-tier privacy & child-safety messaging for transition.`
      },
      {
        code: `KS`, name: `Kansas`, cat: `uncalled`,
        tags: [`uncalled`, `divided`],
        mapGroup: 4,
        govOutcome: `Uncalled (D leads, Holscher; certifies Nov. 15)`,
        legislature: `Divided government if Holscher holds`,
        cioTransition: `Pending certification`,
        techSignals: `Pause data centers · Little said on AI`,
        policySignals: `Attorney General office flipped to D (per DAGA).`,
        sellerNote: `Prepare bipartisan, divided-government pitch focused on measurable cost savings and AG privacy readiness.`
      },
      {
        code: `NC`, name: `North Carolina`, cat: `uncalled`,
        tags: [`uncalled`],
        mapGroup: 4,
        govOutcome: `Governor shares budget pen with flipped House`,
        legislature: `NC House flipped; NC Senate still uncalled`,
        cioTransition: `Legislative leadership transition`,
        techSignals: `Fiscal tightening`,
        policySignals: `Levy-growth and income-tax caps passed—making local revenue less elastic.`,
        sellerNote: `Help local governments stretch tighter budgets via cooperative contracts and grant-braiding.`
      },
      {
        code: `NH`, name: `New Hampshire`, cat: `divided`,
        tags: [`divided`],
        mapGroup: 3,
        govOutcome: `Divided government`,
        legislature: `Divided control (slower, contested budgets)`,
        cioTransition: `Legislative negotiation required`,
        techSignals: `Budgetary caution`,
        policySignals: `Expect slower IT starts and negotiated line items.`,
        sellerNote: `Use existing cooperative contracts and focus on federal-match mandates.`
      },
      {
        code: `AL`, name: `Alabama`, cat: `other`,
        tags: [`other`],
        mapGroup: 3,
        govOutcome: `New incoming governor`,
        legislature: `Republican control`,
        cioTransition: `New executive team`,
        techSignals: `Make data centers pay own way · Little said on AI`,
        policySignals: `Medicaid DOGE-style efficiency reviews active.`,
        sellerNote: `Lead with measurable Medicaid cost savings and error-rate reduction.`
      },
      {
        code: `ME`, name: `Maine`, cat: `other`,
        tags: [`other`],
        mapGroup: 3,
        govOutcome: `New incoming governor`,
        legislature: `State leadership transition`,
        cioTransition: `New executive team`,
        techSignals: `Pause data centers · Little said on AI`,
        policySignals: `Policy-first governance focused on affordability and local impact.`,
        sellerNote: `Sell directly to core policy goals (costs, schools, Medicaid) rather than AI buzzwords.`
      },
      {
        code: `NM`, name: `New Mexico`, cat: `other`,
        tags: [`other`],
        mapGroup: 3,
        govOutcome: `New governor: Deb Haaland (first Native American woman governor; first all-women-of-color ticket)`,
        legislature: `Democratic control`,
        cioTransition: `New executive administration`,
        techSignals: `Pause data centers · Little said on AI`,
        policySignals: `Policy-first focus on resident access, broadband, and rural health.`,
        sellerNote: `Align pitches with BEAD deployment, Rural Health Transformation Program, and fast resident benefits.`
      },
      {
        code: `OK`, name: `Oklahoma`, cat: `other`,
        tags: [`other`],
        mapGroup: 3,
        govOutcome: `New incoming governor`,
        legislature: `Republican control`,
        cioTransition: `New executive team`,
        techSignals: `Make data centers pay own way · Little said on AI`,
        policySignals: `State school chief: Franklin won.`,
        sellerNote: `Engage new school-chief leadership on E-Rate network maintenance and student-data protection.`
      },
      {
        code: `RI`, name: `Rhode Island`, cat: `other`,
        tags: [`other`],
        mapGroup: 3,
        govOutcome: `New incoming governor`,
        legislature: `Democratic control`,
        cioTransition: `New executive team`,
        techSignals: `No stated data-center position · Little said on AI`,
        policySignals: `Policy-first executive agenda.`,
        sellerNote: `Focus on H.R. 1 eligibility readiness and whole-of-state cybersecurity.`
      },
      {
        code: `SC`, name: `South Carolina`, cat: `other`,
        tags: [`other`],
        mapGroup: 3,
        govOutcome: `New incoming governor`,
        legislature: `Republican control`,
        cioTransition: `New executive team`,
        techSignals: `Make data centers pay own way · Little said on AI`,
        policySignals: `Policy-first agenda.`,
        sellerNote: `Anchor on measurable efficiency, cyber resilience, and mandated H.R. 1 compliance.`
      },
      {
        code: `WY`, name: `Wyoming`, cat: `other`,
        tags: [`other`],
        mapGroup: 3,
        govOutcome: `New incoming governor`,
        legislature: `Republican control`,
        cioTransition: `New executive team`,
        techSignals: `Welcome data centers (only incoming gov with this stance) · Little said on AI`,
        policySignals: `Pro-infrastructure siting paired with policy-first IT spending.`,
        sellerNote: `Only incoming governor explicitly welcoming data centers; emphasize transparency to sustain local trust.`
      },
      {
        code: `KY`, name: `Kentucky`, cat: `other`,
        tags: [`other`],
        mapGroup: 3,
        govOutcome: `Active H.R. 1 eligibility execution`,
        legislature: `State budget execution`,
        cioTransition: `Operational agencies`,
        techSignals: `H.R. 1 work funded ($35M)`,
        policySignals: `Early mover on H.R. 1 Medicaid & SNAP modernization.`,
        sellerNote: `Leverage $35M H.R. 1 allocation for verification, renewals, and error-rate reduction tools.`
      },
      {
        code: `MO`, name: `Missouri`, cat: `other`,
        tags: [`other`],
        mapGroup: 3,
        govOutcome: `Active H.R. 1 eligibility execution`,
        legislature: `State budget execution`,
        cioTransition: `Operational agencies`,
        techSignals: `H.R. 1 work funded ($17.9M)`,
        policySignals: `Funded H.R. 1 compliance work ahead of 2027 deadlines.`,
        sellerNote: `Highlight caseworker productivity and SNAP error-rate reduction before Oct. 2027 penalties.`
      },
      {
        code: `LA`, name: `Louisiana`, cat: `uncalled`,
        tags: [`uncalled`],
        mapGroup: 4,
        govOutcome: `Two U.S. House runoffs scheduled Dec. 12`,
        legislature: `Federal delegation runoff`,
        cioTransition: `N/A`,
        techSignals: `Watch federal delegation outcome`,
        policySignals: `Part of the 20 uncalled U.S. House seats.`,
        sellerNote: `Track Dec. 12 runoffs for final House margin before Jan. 3 Congress.`
      }
    ];

    // 21 Incoming Governors Matrix Data
    const MATRIX_CELLS = {
      'cell-pause-guard': [
        { code: `AK`, tier: `guardians`, note: `AK: Pause data centers · Leads with guardrails (Guardians tier).` },
        { code: `MI`, tier: `guardians`, note: `MI: Pause data centers · Leads with guardrails (Guardians tier). New D trifecta.` }
      ],
      'cell-pause-little': [
        { code: `GA`, tier: `policy`, note: `GA: Pause data centers · Little said on AI (Policy-first). Flipped R→D; Bottoms.` },
        { code: `KS`, tier: `policy`, note: `KS: Pause data centers · Little said on AI (Policy-first). Certifies Nov. 15.` },
        { code: `ME`, tier: `policy`, note: `ME: Pause data centers · Little said on AI (Policy-first).` },
        { code: `NM`, tier: `policy`, note: `NM: Pause data centers · Little said on AI (Policy-first). Haaland.` },
        { code: `OH`, tier: `policy`, note: `OH: Pause data centers · Little said on AI (Policy-first). Acton; inaug. Jan. 11.` },
        { code: `TX`, tier: `policy`, note: `TX: Pause data centers · Little said on AI (Policy-first). Hinojosa; inaug. Jan. 19.` }
      ],
      'cell-pay-wants': [
        { code: `CA`, tier: `builders`, note: `CA: Make data centers pay own way · Wants AI for permitting & benefits (Builders tier).` },
        { code: `CO`, tier: `builders`, note: `CO: Make data centers pay own way · Wants AI for public safety & water (Builders tier).` },
        { code: `WI`, tier: `builders`, note: `WI: Make data centers pay own way · Wants AI for efficiency (Builders tier).` }
      ],
      'cell-pay-guard': [
        { code: `IA`, tier: `guardians`, note: `IA: Make data centers pay own way · Leads with guardrails (Guardians tier). Inaug. Jan. 12.` },
        { code: `TN`, tier: `guardians`, note: `TN: Make data centers pay own way · Leads with guardrails (Guardians tier). Blackburn.` }
      ],
      'cell-pay-little': [
        { code: `AL`, tier: `policy`, note: `AL: Make data centers pay own way · Little said on AI (Policy-first).` },
        { code: `FL`, tier: `policy`, note: `FL: Make data centers pay own way · Little said on AI (Policy-first). Donalds.` },
        { code: `NV`, tier: `policy`, note: `NV: Make data centers pay own way · Little said on AI (Policy-first). Ford.` },
        { code: `OK`, tier: `policy`, note: `OK: Make data centers pay own way · Little said on AI (Policy-first).` },
        { code: `SC`, tier: `policy`, note: `SC: Make data centers pay own way · Little said on AI (Policy-first).` }
      ],
      'cell-welcome-little': [
        { code: `WY`, tier: `policy`, note: `WY: Welcomes data centers · Little said on AI (Policy-first).` }
      ],
      'cell-none-little': [
        { code: `MN`, tier: `policy`, note: `MN: No stated data-center position · Klobuchar is the ONLY incoming governor pledging to replace legacy benefits systems ("cursors and blinkers").` },
        { code: `RI`, tier: `policy`, note: `RI: No stated data-center position · Little said on AI (Policy-first).` }
      ]
    };

    const TRANSITIONS_DATA = [
      { state: `Texas`, code: `TX`, type: `flip`, lead: `Joe Madden (exec. dir.), Ernest Bailes`, cio: `Tony Sauerhoff (DIR)`, status: `Flipped R→D · Inaug. Jan. 19 · Divided govt`, channel: `Brief DIR & transition team on H.R. 1 & savings` },
      { state: `Georgia`, code: `GA`, type: `flip`, lead: `Jon Keen, transition COO`, cio: `Shawnzia Thomas (GTA)`, status: `Flipped R→D · Divided govt · House flipped`, channel: `Brief GTA & COO on in-flight work & cost to pause` },
      { state: `Ohio`, code: `OH`, type: `flip`, lead: `John Haseley, chair`, cio: `Katrina Flory`, status: `Flipped R→D · Inaug. Jan. 11 · Divided govt`, channel: `Public input portal: govelectacton.com` },
      { state: `Iowa`, code: `IA`, type: `flip`, lead: `Abe Rakov, chief of staff`, cio: `Matt Behrens`, status: `Flipped R→D · Inaug. Jan. 12 · Divided govt`, channel: `Lead with guardrails, fraud audits & Medicaid plan` },
      { state: `Nevada`, code: `NV`, type: `flip`, lead: `Not yet announced`, cio: `Tim Galluzi`, status: `Flipped R→D · New D trifecta`, channel: `Track appointment; align to unified FY2028 budget` },
      { state: `Colorado`, code: `CO`, type: `same`, lead: `Natalie Hanlon Leh, chief of staff`, cio: `Sarah Tuneberg`, status: `Same-party new governor · Builders AI tier`, channel: `Bring public safety & water AI use cases + audits` },
      { state: `Tennessee`, code: `TN`, type: `same`, lead: `Stuart McWhorter, chair`, cio: `Kristin Darby`, status: `Same-party new gov (Blackburn) · Guardians tier`, channel: `Lead with child safety, privacy & DOGE-style savings` }
    ];

    const MESSAGES_DATA = [
      { say: `People get benefits in days, not weeks`, not: `We modernize legacy eligibility systems`, party: `dem` },
      { say: `Lower costs for families and taxpayers`, not: `Digital transformation`, party: `both` },
      { say: `Stops fraud without dropping eligible people`, not: `AI-powered fraud detection`, party: `gop` },
      { say: `Your data stays private; people make the decisions`, not: `Cutting-edge AI`, party: `both` },
      { say: `Proven in a peer state, with results this budget cycle`, not: `Pilot our new platform`, party: `gop` }
    ];

    const DEFAULT_CHECKLIST = [
      { id: `c1`, horizon: `30 Days`, text: `Brief transition teams in GA, OH, IA, TX, CO, TN (and submit via govelectacton.com in OH) on what is in flight, what it saves, and what pausing costs.` },
      { id: `c2`, horizon: `30 Days`, text: `Close Q4 deals and pull forward renewals using existing cooperative and statewide contracts while new teams settle.` },
      { id: `c3`, horizon: `30 Days`, text: `Protect relationships with career deputy CIOs and agency staff—deputies often become the next CIO.` },
      { id: `c4`, horizon: `90 Days`, text: `Meet newly appointed CIOs after January inaugurations (OH Jan. 11, IA Jan. 12, TX Jan. 19) and attend interim hearings / NASCIO & NASPO forums.` },
      { id: `c5`, horizon: `90 Days`, text: `Monitor State of the State speeches and Day-One executive orders; align pitches to governors' Jan–Feb FY2028 budget proposals.` },
      { id: `c6`, horizon: `90 Days`, text: `Engage counties and cities directly—local governments execute H.R. 1 SNAP/Medicaid changes and shape governors' views.` },
      { id: `c7`, horizon: `180 Days`, text: `Win FY2028 budget line items before the July 1 fiscal-year start and prepare for new RFPs.` },
      { id: `c8`, horizon: `180 Days`, text: `Lead H.R. 1 pitches with mandated error-rate reduction (ahead of Oct. 2027 SNAP cost shifts) and 90%/75% federal Medicaid matches.` },
      { id: `c9`, horizon: `180 Days`, text: `Embed AI disclosure, human review, bias testing, and data-center transparency up front in every proposal.` }
    ];

    // App State
    let appState = {
      selectedState: `OH`,
      mapFilter: `all`,
      selectedGov: null,
      fundingStream: `all`,
      msgFilter: `all`,
      transitionSortKey: `state`,
      transitionSortDir: 1,
      completedChecks: [],
      watchlistNotes: []
    };

    // Persistence Helper
    async function persistState() {
      const statusEl = document.getElementById(`tracker-status`);
      try {
        if (window.notebookAppApi && typeof window.notebookAppApi.setAppState === `function`) {
          await window.notebookAppApi.setAppState({
            selectedState: appState.selectedState,
            completedChecks: appState.completedChecks,
            watchlistNotes: appState.watchlistNotes
          });
          if (statusEl) statusEl.textContent = `Saved`;
        }
      } catch (err) {
        if (statusEl) statusEl.textContent = `Session-only`;
      }
    }

    // 1. Map & State Explorer Functions
    function getFilteredStates() {
      if (appState.mapFilter === `all`) return STATES_DATA;
      if (appState.mapFilter === `flip`) return STATES_DATA.filter(s => s.tags.includes(`flip`));
      if (appState.mapFilter === `trifecta`) return STATES_DATA.filter(s => s.tags.includes(`trifecta`));
      if (appState.mapFilter === `continuity`) return STATES_DATA.filter(s => s.tags.includes(`continuity`));
      if (appState.mapFilter === `divided`) return STATES_DATA.filter(s => s.tags.includes(`divided`));
      if (appState.mapFilter === `uncalled`) return STATES_DATA.filter(s => s.tags.includes(`uncalled`));
      return STATES_DATA;
    }

    function setMapFilter(filterVal) {
      appState.mapFilter = filterVal;
      const btns = document.querySelectorAll(`[data-map-filter]`);
      btns.forEach(btn => {
        btn.setAttribute(`aria-pressed`, btn.dataset.mapFilter === filterVal ? `true` : `false`);
      });
      const visible = getFilteredStates();
      if (visible.length > 0 && !visible.some(s => s.code === appState.selectedState)) {
        appState.selectedState = visible[0].code;
      }
      renderStatePills();
      renderMap();
      renderStateInspector();
    }

    function selectState(code) {
      appState.selectedState = code;
      renderStatePills();
      renderStateInspector();
      persistState();
    }

    function renderStatePills() {
      const container = document.getElementById(`state-pill-strip`);
      if (!container) return;
      const visible = getFilteredStates();
      container.innerHTML = visible.map(s => {
        const isActive = s.code === appState.selectedState ? `active` : ``;
        return `<button type="button" class="state-pill ${isActive}" onclick="selectState('${s.code}')" aria-label="Inspect ${s.name}">${s.code}</button>`;
      }).join(``);
    }

    function renderStateInspector() {
      const state = STATES_DATA.find(s => s.code === appState.selectedState) || STATES_DATA[0];
      const nameEl = document.getElementById(`inspector-state-name`);
      const tagEl = document.getElementById(`inspector-category-tag`);
      const summaryEl = document.getElementById(`inspector-summary`);
      const detailsEl = document.getElementById(`inspector-details`);
      const noteEl = document.getElementById(`inspector-seller-note`);

      if (!nameEl || !tagEl || !summaryEl || !detailsEl || !noteEl) return;

      nameEl.textContent = `${state.name} (${state.code})`;
      const tagMap = {
        flip: { label: `Flipped R→D Gov`, cls: `tag-gold` },
        trifecta: { label: `New D Trifecta`, cls: `tag-slate` },
        continuity: { label: `Continuity / Big Market`, cls: `tag-slate` },
        divided: { label: `Divided Govt`, cls: `tag-warm` },
        uncalled: { label: `Uncalled / Runoff`, cls: `tag-brick` },
        other: { label: `Key SLED Signal`, cls: `tag-pine` }
      };
      const meta = tagMap[state.cat] || tagMap.other;
      tagEl.className = `tag ${meta.cls}`;
      tagEl.textContent = meta.label;

      summaryEl.textContent = state.govOutcome;
      detailsEl.innerHTML = `
        <li><strong>Legislature:</strong> ${state.legislature}</li>
        <li><strong>Transition &amp; CIO:</strong> ${state.cioTransition}</li>
        <li><strong>Data Centers &amp; AI:</strong> ${state.techSignals}</li>
        <li><strong>Ballot, AG &amp; Policy Signals:</strong> ${state.policySignals}</li>
      `;
      noteEl.textContent = state.sellerNote;
    }

    function renderMap() {
      const mapEl = document.getElementById(`us-state-map`);
      if (!mapEl || typeof Plotly === `undefined`) return;

      const visible = getFilteredStates();
      const locations = visible.map(s => s.code);
      const zVals = visible.map(s => s.mapGroup);
      const hoverTexts = visible.map(s => `<b>${s.name} (${s.code})</b><br>${s.govOutcome}<br>${s.legislature}`);

      const cNeutral = getToken(`--map-neutral`);
      const cFlip = getToken(`--data-2`);
      const cBlue = getToken(`--data-1`);
      const cPine = getToken(`--data-3`);
      const cBrick = getToken(`--data-4`);
      const cBg = getToken(`--surface-container-low`);
      const cBorder = getToken(`--map-border`);
      const cText = getToken(`--on-surface`);

      try {
        if (typeof Plotly.setPlotConfig === `function`) {
          Plotly.setPlotConfig({
            topojsonURL: `https://cdn.jsdelivr.net/npm/plotly.js@2.32.0/dist/topojson/`
          });
        }
        const trace = {
          type: `choropleth`,
          locationmode: `USA-states`,
          locations: locations,
          z: zVals,
          text: hoverTexts,
          hoverinfo: `text`,
          showscale: false,
          zmin: 0,
          zmax: 4,
          colorscale: [
            [0, cNeutral],
            [0.24, cNeutral],
            [0.25, cFlip],
            [0.49, cFlip],
            [0.5, cBlue],
            [0.74, cBlue],
            [0.75, cPine],
            [0.99, cPine],
            [1.0, cBrick]
          ],
          marker: { line: { color: cBorder, width: 1.4 } }
        };
        const layout = {
          geo: {
            scope: `usa`,
            bgcolor: cBg,
            lakecolor: cBg,
            landcolor: cNeutral,
            showlakes: true,
            subunitcolor: cBorder
          },
          paper_bgcolor: cBg,
          font: { family: getToken(`--font-body`), color: cText },
          margin: { t: 4, b: 4, l: 4, r: 4 }
        };
        Plotly.newPlot(mapEl, [trace], layout, { responsive: true, displayModeBar: false });
        if (typeof mapEl.on === `function`) {
          mapEl.on(`plotly_click`, function(ev) {
            if (ev && ev.points && ev.points[0] && ev.points[0].location) {
              selectState(ev.points[0].location);
            }
          });
        }
      } catch (e) {
        // Fallback already covered by interactive state pill strip
      }
    }

    // 2. Charts (Chart.js)
    function initCharts() {
      if (typeof Chart === `undefined`) return;
      const c1 = getToken(`--data-1`);
      const c2 = getToken(`--data-2`);
      const c3 = getToken(`--data-3`);
      const cCtx = getToken(`--data-context`);
      const cText = getToken(`--on-surface`);
      const cMuted = getToken(`--on-surface-variant`);
      const cGrid = getToken(`--outline-variant`);
      const monoFont = getToken(`--font-label`);

      // Chart 1: Representation & Turnout Benchmarks
      const repCtx = document.getElementById(`chart-representation`);
      if (repCtx) {
        new Chart(repCtx, {
          type: `bar`,
          data: {
            labels: [`Midterm Turnout (1914+)`, `Statewide Execs (107+)`, `Governors (17 Women)`, `U.S. House (136+ Women)`],
            datasets: [{
              label: `Share / Rate (%)`,
              data: [49.1, 34.5, 34.0, 31.0],
              backgroundColor: [c2, c1, c1, c1],
              borderRadius: 2
            }]
          },
          options: {
            responsive: true,
            maintainAspectRatio: false,
            indexAxis: `y`,
            plugins: {
              legend: { display: false },
              tooltip: { callbacks: { label: function(ctx) { return ` ${ctx.parsed.x}%`; } } }
            },
            scales: {
              x: {
                beginAtZero: true,
                max: 60,
                grid: { color: cGrid },
                ticks: { color: cMuted, font: { family: monoFont, size: 11 }, callback: function(v) { return `${v}%`; } }
              },
              y: {
                grid: { display: false },
                ticks: { color: cText, font: { family: monoFont, size: 11 } }
              }
            }
          }
        });
      }

      // Chart 2: CIO Turnover
      const cioCtx = document.getElementById(`chart-cio-turnover`);
      if (cioCtx) {
        new Chart(cioCtx, {
          type: `bar`,
          data: {
            labels: [`After Party Flip (e.g. GA, IA, NV, OH, TX)`, `After Same-Party Successor (e.g. CO, TN)`],
            datasets: [{
              label: `CIOs Replaced Within 1 Year (%)`,
              data: [69, 60],
              backgroundColor: [c2, cCtx],
              borderRadius: 2
            }]
          },
          options: {
            responsive: true,
            maintainAspectRatio: false,
            indexAxis: `y`,
            plugins: {
              legend: { display: false },
              tooltip: { callbacks: { label: function(ctx) { return ` ${ctx.parsed.x}% replaced within 1 yr`; } } }
            },
            scales: {
              x: {
                beginAtZero: true,
                max: 100,
                grid: { color: cGrid },
                ticks: { color: cMuted, font: { family: monoFont, size: 11 }, callback: function(v) { return `${v}%`; } }
              },
              y: {
                grid: { display: false },
                ticks: { color: cText, font: { family: monoFont, size: 11 } }
              }
            }
          }
        });
      }

      // Chart 3: State AI Laws & Policy Adoption
      const aiCtx = document.getElementById(`chart-ai-laws`);
      if (aiCtx) {
        new Chart(aiCtx, {
          type: `bar`,
          data: {
            labels: [`AI Laws 2025 (46 states)`, `AI Laws H1 2026 (29 states)`, `States w/ AI Policy (%)`, `GenAI Contract Terms (%)`, `Agentic AI Top Priority (%)`],
            datasets: [{
              label: `Count / % Adoption`,
              data: [159, 109, 98, 61, 64],
              backgroundColor: [c1, c2, c3, c3, c2],
              borderRadius: 2
            }]
          },
          options: {
            responsive: true,
            maintainAspectRatio: false,
            plugins: {
              legend: { display: false },
              tooltip: {
                callbacks: {
                  label: function(ctx) {
                    return ctx.dataIndex < 2 ? ` ${ctx.parsed.y} enacted laws` : ` ${ctx.parsed.y}% of states`;
                  }
                }
              }
            },
            scales: {
              y: {
                beginAtZero: true,
                grid: { color: cGrid },
                ticks: { color: cMuted, font: { family: monoFont, size: 11 } }
              },
              x: {
                grid: { display: false },
                ticks: { color: cText, font: { family: monoFont, size: 10 } }
              }
            }
          }
        });
      }
    }

    // 3. 21 Governor Matrix
    function renderGovernorMatrix() {
      Object.keys(MATRIX_CELLS).forEach(cellId => {
        const el = document.getElementById(cellId);
        if (!el) return;
        el.innerHTML = MATRIX_CELLS[cellId].map(item => {
          const isSel = appState.selectedGov === item.code ? `selected` : ``;
          return `<button type="button" class="gov-chip ${isSel}" onclick="selectGovChip('${item.code}')">${item.code}</button>`;
        }).join(``);
      });
    }

    function selectGovChip(code) {
      appState.selectedGov = code;
      renderGovernorMatrix();
      let found = null;
      Object.values(MATRIX_CELLS).forEach(list => {
        list.forEach(item => {
          if (item.code === code) found = item;
        });
      });
      const titleEl = document.getElementById(`gov-persona-title`);
      const descEl = document.getElementById(`gov-persona-desc`);
      if (found && titleEl && descEl) {
        const tierNames = { builders: `Builders Tier`, guardians: `Guardians Tier`, policy: `Policy-First Tier` };
        titleEl.textContent = `${found.code} — ${tierNames[found.tier]}`;
        descEl.textContent = found.note;
      }
      // Also sync the State Inspector if the state is in STATES_DATA
      if (STATES_DATA.some(s => s.code === code)) {
        selectState(code);
      }
    }

    function clearGovernorSelection() {
      appState.selectedGov = null;
      renderGovernorMatrix();
      const titleEl = document.getElementById(`gov-persona-title`);
      const descEl = document.getElementById(`gov-persona-desc`);
      if (titleEl) titleEl.textContent = `Three Seller Archetypes`;
      if (descEl) descEl.textContent = `Select a state chip on the left or review the three tiers below. Note: Minnesota's Amy Klobuchar is the only incoming governor explicitly pledging to replace legacy benefits systems ("they still have cursors and blinkers").`;
    }

    // 4. Transition Table Filtering & Sorting
    function filterTransitions() {
      const searchEl = document.getElementById(`transition-search`);
      const typeEl = document.getElementById(`transition-type`);
      const tbody = document.getElementById(`transition-tbody`);
      if (!tbody) return;

      const q = searchEl ? searchEl.value.trim().toLowerCase() : ``;
      const typeVal = typeEl ? typeEl.value : `all`;

      let rows = TRANSITIONS_DATA.filter(r => {
        const matchesType = typeVal === `all` || r.type === typeVal;
        const blob = `${r.state} ${r.code} ${r.lead} ${r.cio} ${r.status} ${r.channel}`.toLowerCase();
        const matchesQ = !q || blob.includes(q);
        return matchesType && matchesQ;
      });

      rows.sort((a, b) => {
        const va = String(a[appState.transitionSortKey] || ``).toLowerCase();
        const vb = String(b[appState.transitionSortKey] || ``).toLowerCase();
        return va < vb ? -1 * appState.transitionSortDir : va > vb ? 1 * appState.transitionSortDir : 0;
      });

      if (rows.length === 0) {
        tbody.innerHTML = `<tr><td colspan="5">No transition records match your filter. Clear the search box or switch the control filter to view all 7 transition teams.</td></tr>`;
        return;
      }

      tbody.innerHTML = rows.map(r => `
        <tr>
          <td><strong>${r.state}</strong> <span class="tag ${r.type === 'flip' ? 'tag-gold' : 'tag-slate'}">${r.type === 'flip' ? 'Flip R→D' : 'Same Party'}</span></td>
          <td>${r.lead}</td>
          <td class="num-cell">${r.cio}</td>
          <td>${r.status}</td>
          <td>${r.channel}</td>
        </tr>
      `).join(``);
    }

    function sortTransitions(key) {
      if (appState.transitionSortKey === key) {
        appState.transitionSortDir *= -1;
      } else {
        appState.transitionSortKey = key;
        appState.transitionSortDir = 1;
      }
      filterTransitions();
    }

    // 5. Federal Funding Filter
    function setFundingStream(stream) {
      appState.fundingStream = stream;
      const btns = document.querySelectorAll(`[data-stream]`);
      btns.forEach(btn => {
        btn.setAttribute(`aria-pressed`, btn.dataset.stream === stream ? `true` : `false`);
      });
      const cards = document.querySelectorAll(`.funding-panel`);
      cards.forEach(card => {
        if (stream === `all` || card.dataset.streamCard === stream) {
          card.style.display = ``;
        } else {
          card.style.display = `none`;
        }
      });
    }

    // 6. Messaging Filter
    function setMessageFilter(filterVal) {
      appState.msgFilter = filterVal;
      const btns = document.querySelectorAll(`[data-msg-filter]`);
      btns.forEach(btn => {
        btn.setAttribute(`aria-pressed`, btn.dataset.msgFilter === filterVal ? `true` : `false`);
      });
      renderMessages();
    }

    function renderMessages() {
      const tbody = document.getElementById(`messaging-tbody`);
      if (!tbody) return;
      const rows = MESSAGES_DATA.filter(m => {
        if (appState.msgFilter === `all`) return true;
        return m.party === appState.msgFilter || m.party === `both`;
      });
      tbody.innerHTML = rows.map(m => `
        <tr>
          <td><strong>"${m.say}"</strong></td>
          <td style="color: var(--on-surface-variant);">"${m.not}"</td>
        </tr>
      `).join(``);
    }

    // 7. Interactive 30/90/180-Day Tracker & Watchlist
    function renderChecklist() {
      const container = document.getElementById(`checklist-container`);
      const labelEl = document.getElementById(`tracker-progress-label`);
      const fillEl = document.getElementById(`tracker-progress-fill`);
      const barEl = document.getElementById(`tracker-progressbar`);
      if (!container) return;

      const total = DEFAULT_CHECKLIST.length;
      const doneCount = appState.completedChecks.length;
      const pct = Math.round((doneCount / total) * 100);

      if (labelEl) labelEl.textContent = `${doneCount} of ${total} actions completed (${pct}%)`;
      if (fillEl) fillEl.style.width = `${pct}%`;
      if (barEl) barEl.setAttribute(`aria-valuenow`, String(pct));

      container.innerHTML = DEFAULT_CHECKLIST.map(item => {
        const checked = appState.completedChecks.includes(item.id);
        return `
          <div class="checklist-item">
            <button type="button" class="check-btn" role="checkbox" aria-checked="${checked ? 'true' : 'false'}" aria-label="Mark ${item.horizon} task complete" onclick="toggleChecklistItem('${item.id}')">
              ${checked ? '✓' : ''}
            </button>
            <div>
              <span class="tag ${item.horizon === '30 Days' ? 'tag-gold' : item.horizon === '90 Days' ? 'tag-slate' : 'tag-pine'}">${item.horizon}</span>
              <div class="checklist-text ${checked ? 'done' : ''}" style="margin-top: var(--space-xs); font-size: 0.9rem;">${item.text}</div>
            </div>
          </div>
        `;
      }).join(``);
    }

    function toggleChecklistItem(id) {
      const idx = appState.completedChecks.indexOf(id);
      if (idx >= 0) {
        appState.completedChecks.splice(idx, 1);
      } else {
        appState.completedChecks.push(id);
      }
      renderChecklist();
      persistState();
    }

    function renderWatchlistNotes() {
      const listEl = document.getElementById(`watchlist-notes-list`);
      if (!listEl) return;
      if (appState.watchlistNotes.length === 0) {
        listEl.innerHTML = `<li style="color: var(--on-surface-variant); font-size: 0.875rem;">No custom notes added yet. Enter a priority state or agency milestone above to start your account watchlist.</li>`;
        return;
      }
      listEl.innerHTML = appState.watchlistNotes.map((note, idx) => `
        <li style="display: flex; justify-content: space-between; align-items: center; gap: var(--space-sm);">
          <span style="font-size: 0.9rem;">${note}</span>
          <button type="button" class="btn btn-secondary" style="min-height: 44px; padding: 0 var(--space-sm);" onclick="removeWatchlistNote(${idx})" aria-label="Remove note">Remove</button>
        </li>
      `).join(``);
    }

    function addWatchlistNote() {
      const input = document.getElementById(`custom-note-input`);
      const statusEl = document.getElementById(`tracker-status`);
      if (!input) return;
      const val = input.value.trim();
      if (!val) {
        if (statusEl) statusEl.textContent = `Enter a note before adding.`;
        return;
      }
      const safeVal = val.replace(/[<>]/g, ``);
      appState.watchlistNotes.unshift(safeVal);
      input.value = ``;
      renderWatchlistNotes();
      persistState();
    }

    function removeWatchlistNote(idx) {
      appState.watchlistNotes.splice(idx, 1);
      renderWatchlistNotes();
      persistState();
    }

    function resetTracker(btn) {
      if (!btn) return;
      if (btn.dataset.armed !== `true`) {
        btn.dataset.armed = `true`;
        btn.textContent = `Click again to reset`;
        return;
      }
      btn.dataset.armed = `false`;
      btn.textContent = `Reset Tracker`;
      appState.completedChecks = [];
      appState.watchlistNotes = [];
      renderChecklist();
      renderWatchlistNotes();
      persistState();
    }

    function exportActionBrief() {
      const lines = [
        `2026 POST-ELECTION SLED ACTION BRIEF`,
        `Selected State Focus: ${appState.selectedState}`,
        `Completed Playbook Actions (${appState.completedChecks.length}/${DEFAULT_CHECKLIST.length}):`
      ];
      DEFAULT_CHECKLIST.forEach(item => {
        const mark = appState.completedChecks.includes(item.id) ? `[DONE]` : `[OPEN]`;
        lines.push(`${mark} (${item.horizon}) ${item.text}`);
      });
      lines.push(``, `Custom Account & State Watchlist:`);
      if (appState.watchlistNotes.length === 0) {
        lines.push(`(None added)`);
      } else {
        appState.watchlistNotes.forEach(n => lines.push(`- ${n}`));
      }
      const content = lines.join(`\n`);

      if (window.notebookAppApi && typeof window.notebookAppApi.triggerFileDownload === `function`) {
        window.notebookAppApi.triggerFileDownload({
          content: content,
          contentType: `text/plain`,
          encoding: `UTF-8`,
          filename: `sled_post_election_action_brief.txt`
        });
      } else {
        const blob = new Blob([content], { type: `text/plain;charset=utf-8` });
        const url = URL.createObjectURL(blob);
        const a = document.createElement(`a`);
        a.href = url;
        a.download = `sled_post_election_action_brief.txt`;
        a.click();
        URL.revokeObjectURL(url);
      }
    }

    // App Initialization
    async function initDashboard() {
      try {
        if (window.notebookAppApi && typeof window.notebookAppApi.getAppState === `function`) {
          const saved = await window.notebookAppApi.getAppState();
          if (saved) {
            if (saved.selectedState && STATES_DATA.some(s => s.code === saved.selectedState)) {
              appState.selectedState = saved.selectedState;
            }
            if (Array.isArray(saved.completedChecks)) {
              appState.completedChecks = saved.completedChecks;
            }
            if (Array.isArray(saved.watchlistNotes)) {
              appState.watchlistNotes = saved.watchlistNotes;
            }
          }
        }
      } catch (e) {
        // Proceed with defaults if storage unavailable
      }

      renderStatePills();
      renderStateInspector();
      renderMap();
      initCharts();
      renderGovernorMatrix();
      filterTransitions();
      renderMessages();
      renderChecklist();
      renderWatchlistNotes();

      if (window.lucide && typeof window.lucide.createIcons === `function`) {
        window.lucide.createIcons();
      }
      if (window.notebookAppApi && typeof window.notebookAppApi.notifyAppReady === `function`) {
        window.notebookAppApi.notifyAppReady();
      }
    }

    document.addEventListener(`DOMContentLoaded`, initDashboard);
  </script>
</body>
</html>
