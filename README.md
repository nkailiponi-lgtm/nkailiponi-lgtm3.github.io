<svg
  width="360"
  height="420"
  viewBox="0 0 360 420"
  xmlns="http://www.w3.org/2000/svg"
  style="background:#060b1a;border-radius:16px;display:block;margin:auto"
>

  <!-- Stars -->
  <g fill="white" opacity="0.4">
    <circle cx="40" cy="40" r="1"/>
    <circle cx="120" cy="80" r="1"/>
    <circle cx="220" cy="60" r="1"/>
    <circle cx="300" cy="100" r="1"/>
    <circle cx="80" cy="200" r="1"/>
    <circle cx="260" cy="180" r="1"/>
  </g>

  <!-- Enemy Group -->
  <g id="enemies" fill="#ff5f7e">
    <rect x="80" y="60" width="24" height="18" rx="3"/>
    <rect x="120" y="60" width="24" height="18" rx="3"/>
    <rect x="160" y="60" width="24" height="18" rx="3"/>
    <rect x="200" y="60" width="24" height="18" rx="3"/>
    <rect x="240" y="60" width="24" height="18" rx="3"/>

    <animateTransform
      attributeName="transform"
      type="translate"
      from="-20 0"
      to="20 0"
      dur="2s"
      repeatCount="indefinite"
      direction="alternate"
    />
  </g>

  <!-- Player -->
  <rect x="168" y="360" width="24" height="18" rx="3" fill="#7cff7a">
    <animateTransform
      attributeName="transform"
      type="translate"
      from="-40 0"
      to="40 0"
      dur="3s"
      repeatCount="indefinite"
      direction="alternate"
    />
  </rect>

  <!-- Player Bullet -->
  <rect x="180" y="340" width="3" height="10" fill="white">
