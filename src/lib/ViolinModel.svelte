<script>
  // Import Threlte components to display 3D objects
  import { T } from '@threlte/core';

  // Import the GLB model loader
  import { useGltf } from '@threlte/extras';

  // Import Three.js tools for calculating model dimensions
  import { Box3, Vector3, Color  } from 'three';

  // Load the violin model from the static folder
  const gltf = useGltf('/models/Violina_Web_Clean.glb');
  // Receive the selected color from the main page
    let { woodColor = '#ffffff' } = $props();

    // Store the original material color
    let originalColor = null;

    // Remember the body whose material was copied
    let preparedBody = null;

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

    // Make the violin slightly larger in the viewer
    const scale = maxSize > 0 ? 4.5 / maxSize : 1;

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

  // Change only the wooden body color
  $effect(() => {

    // Get the loaded violin scene
    const scene = $gltf?.scene;

    // Get the selected color
    const selectedColor = woodColor;

    // Stop if the model is not loaded
    if (!scene) return;

    // Find the violin body
    const body = scene.getObjectByName('KMN_Govde_LP');

    // Stop if the body has no material
    if (!body || !body.material) return;

    // Create a separate material for the body
    if (preparedBody !== body) {
      body.material = body.material.clone();

      // Save its original color
      originalColor = body.material.color.clone();

      preparedBody = body;
    }

    // Create a new color
    const tint = new Color(selectedColor);

    // Apply the tint without removing the texture
    body.material.color.copy(originalColor).multiply(tint);

    // Update the material
    body.material.needsUpdate = true;
  });

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
