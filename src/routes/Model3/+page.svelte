<script>
    import * as THREE from "three";
    import GeoScene from "$lib/geoScene.svelte";
    let points = [
      
      {
        position: new THREE.Vector3(0.15, 0.7, -0.6),
        canvasCordinates: new THREE.Vector2(0, 0),
        label: "Demo",
        text: "Click to watch a demo video",
        link: "https://www.youtube.com/embed/bcB4zxubK-0",
      },
    ];
  
    let models = ["GeologischeAbfolgeBeispiel.gltf","GeologischeAbfolgeBeispiel2.gltf"];
  
    let cameraInitialPosition = new THREE.Vector3(25,15,3);

  
    let light_type = "3DModel" /*DroneImageScene or 3DModel*/
  
  
  </script>
  
  
  
  <GeoScene points = {points} models = {models} cameraInitialPosition = {cameraInitialPosition} light_type = {light_type}/>
  
  
  