<script>
  import { browser } from "$app/environment";
  import * as THREE from "three";
  import { getAllContexts, onMount } from "svelte";

  import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
  import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader.js";
  import RangeSlider from 'svelte-range-slider-pips';

  //import { gsap } from "gsap";

  let root;

  let toggle = false;
  let curentVideo = "";

  function openVideo(link) {
    toggle = true;
    curentVideo = link;
  }

  function closeWindow() {
    toggle = false;
  }

  //timing function

  let values = [10];
  
  let playbackspeed = 0.05;

  let isPlaying = false;

  function stopAnim() {
    isPlaying = false;
  }
  function playAnim() {
    isPlaying =true;
  }
  function slowAnim() {
    playbackspeed *= 0.5;
  }
  function fastAnim() {
    playbackspeed *= 2;
  }

  



  

  export let points = [];
  export let cameraInitialPosition = new THREE.Vector3(0, 3, 5);
  export let scene_background_color = "#ffffff";
  export let light_type = "";

  export let models = [];
      //only works when file is in static/models

  onMount(() => {
    const canvas = root.querySelector(".webgl");
    // Do something with nd, such as adding event listeners, styles, etc.

    if (browser) {
      const loadingManager = new THREE.LoadingManager();

      const gltfLoader = new GLTFLoader(loadingManager);
      const cubeTextureLoader = new THREE.CubeTextureLoader(loadingManager);

      const scene = new THREE.Scene();

      /**
       * Background
       */
      const params = {
        color: scene_background_color,
      };
      scene.background = new THREE.Color(params.color);

      /**
       * Models
       */

      let mixer = null

      for (const block of models) {
        console.log(block);
        //add /TapRepWeb bzw /Formenschatztruhewhen deploying
        gltfLoader.load("./models/" + block , (gltf) => {

          mixer = new THREE.AnimationMixer(gltf.scene)
          const action = mixer.clipAction(gltf.animations[0])

          action.play()

          gltf.scene.scale.set(1, 1, 1);
          gltf.scene.rotation.set(0, 0, 0);

          scene.add(gltf.scene);
          

          updateAllMaterials();
        });
      }


      const camera = new THREE.PerspectiveCamera(
        75,
        window.innerWidth / window.innerHeight,
        0.1,
        1000
      );

      /**
       * Points of interest
       */
      const raycaster = new THREE.Raycaster();

      /**
       * Lights
      */

      
      if (light_type == "3DModel") {
        const directionalLight = new THREE.DirectionalLight("#ffffff", 3);
        directionalLight.castShadow = false;
        directionalLight.shadow.camera.far = 15;
        directionalLight.shadow.mapSize.set(1024, 1024);
        directionalLight.shadow.normalBias = 0.05;
        directionalLight.position.set(0.25, 3, -2.25);
        scene.add(directionalLight);
      } else if (light_type == "DroneImageScene") {
        const light = new THREE.AmbientLight( 0x404040 ); // soft white light
        light.intensity = 25
        scene.add( light );
      }

      
     

      

      // Controls
      const controls = new OrbitControls(camera, canvas);
      controls.enableDamping = true;

      /**
       * Renderer
       */

      const renderer = new THREE.WebGLRenderer({
        canvas: canvas,
        antialias: true,
      });

      renderer.toneMapping = THREE.ReinhardToneMapping;
      renderer.toneMappingExposure = 3;
      renderer.shadowMap.enabled = true;
      renderer.shadowMap.type = THREE.PCFSoftShadowMap;

      renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));

      renderer.setSize(window.innerWidth, window.innerHeight);

      /**
       * Sizes
       */

      const sizes = {
        width: window.innerWidth,
        height: window.innerHeight,
      };

      
  

      
      window.addEventListener("resize", () => {
        // Update sizes
        sizes.width = window.innerWidth;
        sizes.height = window.innerHeight;

        // Update camera
        camera.aspect = sizes.width / sizes.height;
        camera.updateProjectionMatrix();

        // Update renderer
        renderer.setSize(sizes.width, sizes.height);
        renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
      });

      camera.position.x = cameraInitialPosition.x;
      camera.position.y = cameraInitialPosition.y;
      camera.position.z = cameraInitialPosition.z;

      
      const tick = () => {
        //const elapsedTime = clock.getElapsedTime()
        //const deltaTime = elapsedTime - previousTime
        //previousTime = elapsedTime

        controls.update();

        //mixer
        
        if(mixer != null && isPlaying){
          if(mixer.time > 10) { mixer.setTime(0)}
          mixer.update(playbackspeed);
          values[0]= mixer.time
        }


        if(mixer != null && !isPlaying){
          mixer.setTime(values);
        }

        
        

        for (const point of points) {
          // Get 2D screen position
          const screenPosition = point.position.clone();
          screenPosition.project(camera);
          const translateX = (0.5 + screenPosition.x * 0.5) * sizes.width;
          const translateY =
            (1 - (0.5 + screenPosition.y * 0.5)) * sizes.height;
          point.canvasCordinates.set(translateX, translateY);

          points = points; //normally completely unnecessary, but it triggers a rerender in svelte
        }

        renderer.render(scene, camera);
        window.requestAnimationFrame(tick);
      };

      tick();
    }
  });


  function onGrab() {
    isPlaying = false
  }

  


</script>



<div bind:this={root} class="bind-div">
  
    <div class = "controlbox">
        <RangeSlider springValues={{ stiffness: 1, damping: 1 }} step={0.1} min={0} max={10} bind:values
        on:start={((e) => { onGrab() })}
        />
      <div class ="animationtools">
        <div class = "toolbutton" on:click={() => stopAnim()}>stop</div>
        <div class = "toolbutton" on:click={() => playAnim()}>play</div>
        <div class = "toolbutton" on:click={() => slowAnim()}>slow</div>
        <div class = "toolbutton" on:click={() => fastAnim()}>fast</div>

      </div>
    
    </div>

    <canvas class="webgl"></canvas>
    
    <!--
    {#each points as point, i}
      <div
        class="point"
        style="left: {point.canvasCordinates.x}px; top: {point.canvasCordinates
          .y}px"
      >
        {#if point.link}
          <div class="label" on:click={() => openVideo(point.link)}>
            {point.label}
          </div>
        {:else}
          <div class="label">{point.label}</div>
        {/if}
        <div class="text">{point.text}</div>
      </div>
    {/each}

    {#if toggle}
      <div id="PopUp" class="PopUp">
        <button class="close" on:click={closeWindow}>x</button>
        <iframe id="iframeVid" src={curentVideo}> </iframe>
      </div>
    {/if}
    -->
  
</div>

<style>
  body {
    height: 100%;
    overflow: hidden;
  }
  .webgl {
    position: static;
    top: 0;
    left: 0;
    outline: none;
    z-index: -10;
    height: 100%;
  }

  .point {
    position: absolute;
    font-size: 8pt;
    /* pointer-events: none; */
    z-index: 10;
    background-color: rgba(220, 220, 220, 0.594);
    padding: 0.5rem;
    border-radius: 0.5rem;
    -webkit-user-select: none; /* Safari */
    -ms-user-select: none; /* IE 10 and IE 11 */
    user-select: none; /* Standard syntax */
  }

  .point .label {
    user-select: none;
  }

  .point .text {
    opacity: 0;
    height: 0;
    width: 0;
    user-select: none;
  }

  .point:hover .text {
    opacity: 1;
    height: auto;
    width:  300px;
    user-select: none;
  }

  .PopUp {
    position: absolute;
    background-color: gainsboro;
    padding: 1rem;
    top: 20%;
    left: 20%;
    width: 60%;
    height: 60%;
    z-index: -20;
    pointer-events: none;
    border-radius: 1rem;
    opacity: 1;
    pointer-events: auto;
    z-index: 30;
    user-select: none;
  }
  iframe {
    display: block;
    width: 100%;
    height: 100%;
    margin-left: auto;
    margin-right: auto;
    border: none;
  }

  .close {
    position: absolute;
    top: 0;
    right: 0;
    background-color: red;
    color: white;
    border: none;
    padding: 0.5rem;
    border-radius: 0.5rem;
  }

  .playbutton {

  }
  .controlbox{
    
    position: absolute;
    flex-direction: column;
    background-color: rgb(175, 175, 201);
    bottom: 15%;
    left: 40%;
    width: 20%;
    height: 10%;
    z-index: 5;

  }
  .animationtools{
    display: flex;
    flex-direction: row;
    align-items: center;
    
  }

  .toolbutton{
    width: 50px;
    text-align: center;
    background-color: rgb(209, 194, 194);
    cursor: default;
  }
  .toolbutton:hover{
    background-color: rgb(153, 173, 191);
  }

</style>
