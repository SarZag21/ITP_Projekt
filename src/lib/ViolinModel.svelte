<script>
  // Import Threlte components to display 3D objects
  import { T, useThrelte } from '@threlte/core';

  // Import the GLB model loader
  import { useGltf } from '@threlte/extras';

  // Import Three.js tools for calculating model dimensions
  import { Box3, Vector3} from 'three';

// Load the violin model with the chinrest Shape Key
const gltf = useGltf('/models/Violina_WithShape.glb');
  // Get the function that refreshes the 3D scene
  const { invalidate } = useThrelte();
 // Receive color, finish and chinrest width from the main page
let {
  woodColor = '#ffffff',
  woodFinish = 'glossy',
  chinrestWidth = 0
} = $props();

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

 
  // Store the original materials of wooden parts
  const originalMaterials = new Map();

  // Update the color and finish of the violin's wooden parts
  $effect(() => {

    // Get the loaded violin scene
    const scene = $gltf?.scene;

    // Read the selected configuration
    const selectedColor = woodColor;
    const selectedFinish = woodFinish;
    // Stop if the model is not loaded
    if (!scene) return;

    // Wooden parts that should change together
    const woodenParts = [
      'KMN_Govde_LP', // Violin body
      'KMN_Sap_LP'    // Violin neck
    ];

    // Material finish options
    const finishes = {
      glossy: 0.05,
      matte: 0.90
    };

    // Update each wooden part
    woodenParts.forEach((name) => {

      // Find the object by its Blender name
      const part = scene.getObjectByName(name);

      // Skip objects that are missing
      if (!part || !part.material) return;

      // Prepare a separate material for this object
      if (!originalMaterials.has(part)) {

        // Save the original material color
        const originalColor = part.material.color.clone();

        // Clone the material to avoid changing other objects
        part.material = part.material.clone();

        // Remember the original color
        originalMaterials.set(part, originalColor);
      }

      // Get the original color
      const originalColor = originalMaterials.get(part);

      // Restore the original color or apply a selected tint
      if (selectedColor === '#ffffff') {
        part.material.color.copy(originalColor);
      } else {
        part.material.color.set(selectedColor);
      }

      // Apply the selected material finish
      part.material.roughness = finishes[selectedFinish] ?? 0.4;
      part.material.metalness = 0;


      // Add a polished varnish effect to the glossy finish
      if ('clearcoat' in part.material) {
        part.material.clearcoat = selectedFinish === 'glossy' ? 1.0 : 0.0;
        part.material.clearcoatRoughness =
          selectedFinish === 'glossy' ? 0.05 : 1.0;
      }


      // Update the material
      part.material.needsUpdate = true;
    });

    // Refresh the 3D scene immediately
    invalidate();
  });


  // Update the chinrest shape using its Morph Target
  $effect(() => {

    // Get the loaded violin scene
    const scene = $gltf?.scene;

    // Read the selected chinrest width
    const width = chinrestWidth;

    // Stop if the model is not loaded
    if (!scene) return;

    // Find meshes that contain the Chinrest_Wide morph target
    scene.traverse((object) => {

      // Skip objects without morph targets
      if (!object.isMesh || !object.morphTargetDictionary) return;

      // Find the morph target index
      const index = object.morphTargetDictionary['Chinrest_Wide'];

      // Stop if this mesh does not have the target
      if (index === undefined) return;

      // Apply the slider value between 0 and 1
      object.morphTargetInfluences[index] = width;

    });

    // Refresh the 3D scene immediately
    invalidate();
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
