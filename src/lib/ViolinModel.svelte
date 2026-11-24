
<script>
  // Import Threlte components to display 3D objects
  import { T } from '@threlte/core';

  // Import the GLB model loader
  import { useGltf } from '@threlte/extras';

  // Import Three.js tools for calculating model dimensions
  import { Box3, Vector3 } from 'three';

  // Load the violin model from the static folder
  const gltf = useGltf('/models/Violina_Web_Clean.glb');

  // Calculate the correct position and size of the violin
  function getTransform(scene) {

    // Use default values while the model is loading
    if (!scene) {
      return {
        scale: 1,
        position: [0, 0, 0]
      };
    }

    // Calculate the bounding box around the model
    const box = new Box3().setFromObject(scene);

    // Get the width, height and depth of the model
    const size = box.getSize(new Vector3());

    // Find the center of the model
    const center = box.getCenter(new Vector3());

    // Find the largest dimension of the model
    const maxSize = Math.max(size.x, size.y, size.z);

    // Scale the model to fit inside the 3D viewer
    const scale = maxSize > 0 ? 4 / maxSize : 1;

    // Return the new scale and centered position
    return {
      scale,
      position: [
        -center.x * scale,
        -center.y * scale,
        -center.z * scale
      ]
    };
  }

  // Automatically update the transform when the model loads
  let transform = $derived(getTransform($gltf?.scene));
</script>

<!-- Render the violin after the GLB model has loaded -->
{#if $gltf}
  <!-- Group the violin objects together -->
  <T.Group>
    <!-- Display the model with the calculated position and scale -->
    <T
      is={$gltf.scene}
      scale={transform.scale}
      position={transform.position}
    />
  </T.Group>
{/if}
