
<script>
  // Import the main 3D components from Threlte
  import { Canvas, T } from '@threlte/core';

  // Import mouse controls for rotating and zooming
  import { OrbitControls } from '@threlte/extras';

  // Import the violin model component
  import ViolinModel from '#lib/ViolinModel.svelte';

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

    <!-- Container for the interactive 3D scene -->
    <div class="viewer">

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
        <ViolinModel woodColor={selectedColor} />

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

    <!-- Explain the available mouse controls -->
    <div class="viewer-instructions">
      <span>↻ Drag to rotate</span>
      <span>＋ Scroll to zoom</span>
      <span>✥ Right-click to move</span>
    </div>

    <!-- Additional information about the violin -->
    <div class="description">
      <h3>Crafted with Precision</h3>
      <p>
        Explore the violin from every angle.
        Rotate, zoom and discover its
        unique wooden details.
      </p>
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

  /* Arrange the mouse instructions */
  .viewer-instructions {
    display: flex;
    justify-content: center;
    flex-wrap: wrap;
    gap: 28px;
    margin-top: 22px;
    color: #9a7c75;
    font-size: 13px;
  }

  /* Center the description below the viewer */
  .description {
    max-width: 500px;
    margin: 55px auto 0;
  }

  /* Style the description heading */
  .description h3 {
    font-family: Georgia, serif;
    font-size: 26px;
    font-weight: 400;
    color: #68273a;
    margin-bottom: 12px;
  }

  /* Style the description paragraph */
  .description p {
    font-size: 15px;
    line-height: 1.8;
    color: #8d7771;
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

    /* Reduce control instructions spacing */
    .viewer-instructions {
      gap: 14px;
      font-size: 12px;
    }
  }
</style>
