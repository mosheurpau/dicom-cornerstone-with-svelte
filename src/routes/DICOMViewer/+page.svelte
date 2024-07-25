<script>
  import { onMount } from "svelte";
  let element;

  onMount(async () => {
    // Import Cornerstone libraries dynamically to avoid SSR issues
    const { default: cornerstone } = await import("cornerstone-core");
    const { default: cornerstoneTools } = await import("cornerstone-tools");
    const { default: cornerstoneWADOImageLoader } = await import(
      "cornerstone-wado-image-loader"
    );
    const { default: dicomParser } = await import("dicom-parser");

    // Configure Cornerstone WADO Image Loader
    cornerstoneWADOImageLoader.external.cornerstone = cornerstone;
    cornerstoneWADOImageLoader.external.dicomParser = dicomParser;

    // Enable Cornerstone on the element
    cornerstone.enable(element);

    // Load and display the DICOM image
    const imageId = "wadouri:/dicom/im.dcm"; // Local DICOM file path
    try {
      const image = await cornerstone.loadImage(imageId);
      cornerstone.displayImage(element, image);
      cornerstoneTools.mouseInput.enable(element);
      cornerstoneTools.mouseWheelInput.enable(element);
      cornerstoneTools.wwwc.activate(element, 1); // ww/wc is the default tool for 'mousedown'
    } catch (error) {
      console.error("Error loading and displaying image:", error);
    }
  });
</script>

<div bind:this={element} id="dicomImage"></div>

<style>
  #dicomImage {
    width: 512px;
    height: 512px;
    border: 1px solid black;
  }
</style>
