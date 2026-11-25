<script>
  // Import the main 3D components from Threlte
  import { Canvas, T } from '@threlte/core';

  // Import mouse controls for rotating and zooming
  import { OrbitControls } from '@threlte/extras';

  // Import the violin model component
  import ViolinModel from '#lib/ViolinModel.svelte';

  // Store the selected material finish
  let selectedFinish = $state('glossy');

  // Store the currently selected wood color
  let selectedColor = $state('#ffffff');

  // Available violin colors
  const colors = [
    {
      name: 'Original Amber',
      value: '#ffffff',
      preview: '#C58A4D'
    },
    {
      name: 'Reddish Brown',
      value: '#E6A896',
      preview: '#9F6242'
    },
    {
      name: 'Dark Walnut',
      value: '#AE8975',
      preview: '#79513E'
    }
  ];

// Store the selected chinrest width
let chinrestWidth = $state(0);


// Track whether the 3D violin is loading
let isLoading = $state(true);


</script>

<!-- Define the website title and description -->
<svelte:head>
  <title>Violina | 3D Configurator</title>
  <meta
    name="description"
    content="Interactive 3D Violin Configurator"
  />
</svelte:head>

<!-- Main website container -->
<main>

  <!-- Website navigation and logo -->
  <header class="navbar">
    <div class="brand">
      <h1>Violina</h1>
      <span>3D Configurator</span>
    </div>
  </header>

  <!-- Main content section -->
  <section class="hero">

    <!-- Introduction above the 3D viewer -->
    <div class="hero-heading">
      <h2>Explore Your Violin</h2>
      <p>
        Discover every detail of a beautifully
        crafted violin in interactive 3D.
      </p>
    </div>

    <!-- Arrange the 3D viewer and controls side by side -->
    <div class="configurator-layout">

    <!-- Container for the interactive 3D scene -->
    <div class="viewer">


    <!-- Show the loading indicator while the model loads -->
    {#if isLoading}
      <div class="loading-overlay">
        <div class="loading-spinner"></div>
        <p>Loading Violin...</p>
      </div>
    {/if}


      <!-- Create the 3D canvas -->
      <Canvas>

        <!-- Set the camera position and perspective -->
        <T.PerspectiveCamera
          makeDefault
          position={[0, 0, 6.5]}
          fov={45}
          near={0.1}
          far={100}
        />

        <!-- Add general lighting to the scene -->
        <T.AmbientLight
          intensity={1.2}
        />

        <!-- Main white light from the front -->
        <T.DirectionalLight
          position={[3, 5, 6]}
          intensity={2}
          color="#ffffff"
        />

        <!-- Add a softer warm light from the side -->
        <T.DirectionalLight
          position={[-4, 2, 4]}
          intensity={0.8}
          color="#fff1df"
        />

        <!-- Add back lighting to highlight the edges -->
        <T.DirectionalLight
          position={[0, 3, -5]}
          intensity={1}
          color="#ffffff"
        />

        <!-- Display the imported violin model -->
        <!-- Pass the selected color to the violin model -->
        <!-- Pass the selected color and finish to the violin -->
      <ViolinModel
  woodColor={selectedColor}
  woodFinish={selectedFinish}
  chinrestWidth={chinrestWidth}
  onReady={() => isLoading = false}
/>

        <!-- Allow users to rotate, zoom and move the camera -->
        <OrbitControls
          enableDamping
          enableRotate
          enableZoom
          enablePan
          minDistance={2.5}
          maxDistance={12}
        />

      </Canvas>

    </div>
    <!-- Panel for changing the violin color -->
    <div class="color-panel">

    <!-- Panel heading -->
    <h3>Customize Your Violin</h3>

    <!-- Color selection heading -->
    <h4>Wood Color</h4>
  

    <!-- Short description -->
    <p>Choose your preferred violin color.</p>

    <!-- Container for color buttons -->
    <div class="color-options">

        <!-- Create one button for each color -->
        {#each colors as color}

        <button
            type="button"
            class:active={selectedColor === color.value}
            onclick={() => selectedColor = color.value}
            aria-pressed={selectedColor === color.value}
        >

            <!-- Display the color preview -->
            <span
            class="color-circle"
            style:background={color.preview}
            ></span>

            <!-- Display the color name -->
            <span>{color.name}</span>

        </button>

  
    
        {/each}

    </div>

<!-- Material finish selection -->
<div class="finish-section">

  <!-- Section heading -->
  <h4>Material Finish</h4>
  <p>Choose the surface appearance.</p>

  <!-- Finish buttons -->
  <div class="finish-options">

    <!-- Glossy finish -->
    <button
      type="button"
      class:active={selectedFinish === 'glossy'}
      onclick={() => selectedFinish = 'glossy'}
      aria-pressed={selectedFinish === 'glossy'}
    >
      Glossy
    </button>

    <!-- Matte finish -->
    <button
      type="button"
      class:active={selectedFinish === 'matte'}
      onclick={() => selectedFinish = 'matte'}
      aria-pressed={selectedFinish === 'matte'}
    >
      Matte
    </button>

  </div>
</div>


<!-- Chinrest geometry configuration -->
<div class="chinrest-section">

  <!-- Section heading -->
  <h4>Chinrest Shape</h4>
  <p>Adjust the width of the chinrest.</p>

  <!-- Shape slider -->
  <input
    type="range"
    min="0"
    max="1"
    step="0.01"
    bind:value={chinrestWidth}
  />

  <!-- Display selected width -->

  <div class="chinrest-labels">
    <span>Standard</span>
    <span>{Math.round(chinrestWidth * 100)}%</span>
    <span>Wider</span>
  </div>

</div>

<!-- Classical violin music player -->
<div class="sound-section">
  <h4>Listen to the Violin</h4>

  <p>Vivaldi-The Four Seasons: Spring</p>

  <!-- Play and pause the classical music -->
  <audio controls preload="none">
    <source
      src="/sounds/vivaldi_spring.ogg"
      type="audio/ogg"
    />
    Your browser does not support audio.
  </audio>

  <p class="music-credit">
    Performance: The Modena Chamber Orchestra
  </p>

</div>
</div>
    </div>
  </section>
</main>

<style>
  /* Include padding and borders in element dimensions */
  :global(*) {
    box-sizing: border-box;
  }

  /* Define the default page background and font */
  :global(body) {
    margin: 0;
    background: #f8f2ee;
    color: #312321;
    font-family: Arial, sans-serif;
  }

  /* Make the main page fill the screen */
  main {
    min-height: 100vh;
  }

  /* Style the navigation bar */
  .navbar {
    width: 100%;
    padding: 22px 8%;
    background: #fffaf7;
    border-bottom: 1px solid #eadfd9;
  }

  /* Arrange the logo and subtitle horizontally */
  .brand {
    display: flex;
    align-items: baseline;
    gap: 16px;
  }

  /* Style the main logo */
  .brand h1 {
    margin: 0;
    font-family: Georgia, serif;
    font-size: 35px;
    font-weight: 500;
    color: #6d2439;
  }

  /* Style the text next to the logo */
  .brand span {
    font-size: 14px;
    color: #9a7976;
  }

  /* Center the main content */
  .hero {
    padding: 40px 20px 70px;
    text-align: center;
  }

  /* Style the section heading */
  .hero-heading h2 {
    font-family: Georgia, serif;
    font-size: 36px;
    font-weight: 400;
    margin: 0 0 12px;
    color: #382526;
  }

  /* Style the introductory paragraph */
  .hero-heading p {
    font-size: 15px;
    color: #927b73;
    line-height: 1.7;
    margin-bottom: 30px;
  }

  /* Define the size and background of the 3D viewer */
  .viewer {
    width: min(100%, 1050px);
    height: 650px;
    margin: 0 auto;
    position: relative;
    overflow: hidden;
    border-radius: 22px;
    background: radial-gradient(
      ellipse at center,
      #fffaf7 0%,
      #f0e3db 70%,
      #e6d6cc 100%
    );
    border: 1px solid #eadcd5;
    box-shadow: 0 12px 40px rgba(80, 45, 32, 0.06);
  }

  /* Make the 3D canvas fill its container */
  .viewer :global(canvas) {
    display: block;
    width: 100%;
    height: 100%;
    cursor: grab;
  }

  /* Change the cursor while dragging */
  .viewer :global(canvas:active) {
    cursor: grabbing;
  }

  /* Adjust the layout for smaller screens */
  @media (max-width: 768px) {

    /* Reduce navigation spacing */
    .navbar {
      padding: 18px 6%;
    }

    /* Reduce the logo size */
    .brand h1 {
      font-size: 29px;
    }

    /* Reduce section spacing */
    .hero {
      padding: 30px 12px;
    }

    /* Reduce the heading size */
    .hero-heading h2 {
      font-size: 28px;
    }

    /* Adjust viewer height for mobile devices */
    .viewer {
      height: 520px;
      border-radius: 16px;
    }
  }
/* Style the color configuration panel */
.color-panel {
  max-width: 600px;
  margin: 40px auto;
  padding: 25px;
  background: #fffaf7;
  border: 1px solid #eadcd5;
  border-radius: 18px;
  text-align: left;
}

/* Style the configuration heading */
.color-panel h3 {
  font-family: Georgia, serif;
  font-size: 26px;
  font-weight: 400;
  color: #68273a;
}

/* Style the color selection heading */
.color-panel h4 {
  font-size: 16px;
  color: #49332f;
}

/* Style the panel description */
.color-panel p {
  color: #978078;
  font-size: 14px;
}

/* Arrange the color buttons */
.color-options {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
  margin-top: 20px;
}

/* Style each color button */
.color-options button {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 12px 16px;
  background: white;
  border: 1px solid #e5d7cf;
  border-radius: 10px;
  cursor: pointer;
  color: #4d3934;
}

/* Highlight the selected color */
.color-options button.active {
  border-color: #8c4054;
  background: #faf0f0;
}

/* Display the color preview circles */
.color-circle {
  width: 25px;
  height: 25px;
  border-radius: 50%;
  border: 1px solid rgba(0, 0, 0, 0.12);
  flex-shrink: 0;
}

/* Add a hover effect to the buttons */
.color-options button:hover {
  border-color: #ad7c86;
}


/* Material finish section */
.finish-section {
  margin-top: 30px;
  padding-top: 25px;
  border-top: 1px solid #eadcd5;
}

/* Arrange finish buttons */
.finish-options {
  display: flex;
  gap: 12px;
  margin-top: 15px;
}

/* Style Glossy and Matte buttons */
.finish-options button {
  padding: 12px 22px;
  background: white;
  border: 1px solid #e5d7cf;
  border-radius: 10px;
  color: #4d3934;
  cursor: pointer;
}

/* Highlight selected finish */
.finish-options button.active {
  border-color: #8c4054;
  background: #faf0f0;
}

/* Chinrest shape section */
.chinrest-section {
  margin-top: 30px;
  padding-top: 25px;
  border-top: 1px solid #eadcd5;
}

/* Chinrest slider */
.chinrest-section input[type="range"] {
  width: 100%;
  margin-top: 18px;
  accent-color: #8c4054;
  cursor: pointer;
}

/* Slider labels */
.chinrest-labels {
  display: flex;
  justify-content: space-between;
  margin-top: 10px;
  font-size: 12px;
  color: #927a73;
}

/* Classical music section */
.sound-section {
  margin-top: 30px;
  padding-top: 25px;
  border-top: 1px solid #eadcd5;
}

/* Music player */
.sound-section audio {
  width: 100%;
  max-width: 100%;
  margin-top: 15px;
}

/* Music attribution */
.music-credit {
  font-size: 12px;
  color: #927a73;
}


/* Cover the 3D viewer while loading */
.loading-overlay {
  position: absolute;
  inset: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 15px;
  background: #fffaf7;
  z-index: 10;
}

/* Animate the loading circle */
.loading-spinner {
  width: 45px;
  height: 45px;
  border: 4px solid #eadcd5;
  border-top-color: #8c4054;
  border-radius: 50%;
  animation: spin 1s linear infinite;
}

/* Style the loading message */
.loading-overlay p {
  color: #68273a;
  font-size: 15px;
  font-weight: 500;
}

/* Rotate the loading circle */
@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}


/* Display the violin and configuration panel side by side */
.configurator-layout {
  display: grid;
  grid-template-columns: minmax(0, 1.5fr) minmax(0, 1fr);
  gap: 24px;
  align-items: start;
  max-width: 1450px;
  margin: 0 auto;
}

/* Make the violin viewer fit its grid column */
.configurator-layout .viewer {
  width: 100%;
  height: 650px;
  margin: 0;
}

/* Make the control panel fit its grid column */
.configurator-layout .color-panel {
  width: 100%;
  max-width: none;
  margin: 0;
}

/* Stack the viewer and controls on smaller screens */
@media (max-width: 1000px) {
  .configurator-layout {
    grid-template-columns: 1fr;
  }

  .configurator-layout .viewer {
    height: 520px;
  }
}

/* Make the 3D viewer smaller on phones */
@media (max-width: 600px) {
  .configurator-layout .viewer {
    height: 420px;
  }

  .configurator-layout .color-panel {
    padding: 20px;
  }

  .music-credit {
  font-size: 12px;
  color: #927a73;
}
}
  
</style>
