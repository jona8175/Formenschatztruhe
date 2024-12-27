<script>
  import { onMount } from 'svelte';

  // Reactive variables to hold the layerId and featureId
  let layerId = '';
  let featureId = '';
  let string1 = '';
  

  // Function to handle messages from the iframe
  function handleMessage(event) {
    if (event.data.type === 'gaFeatureSelection') {
      layerId = event.data.payload['layerId'];
      featureId = event.data.payload['featureId'];
      
      switch (featureId) { 
        case "2176": 
          string1 = "x";
          window.location.href = "http://www.w3schools.com";
          break; 
        case "2": 
          string1 = "xy"; 
          break; 
        case "3": 
          string1 = "xyz"; 
          break; // Add more cases as needed 
        case "2099": 
          string1 = "Unterhard"; 
          break; 
        default: 
          string1 = "nothing"; // Optional: Define a default case break; }
      }
    }  
      
  }

  // Setup the event listener when the component mounts
  onMount(() => {
    window.addEventListener('message', handleMessage);

    return () => {
      window.removeEventListener('message', handleMessage);
    };
  });
  
  
</script>

<style>
  #map-container {
    width: 100%;
    height: 400px;
  }

  .info {
    margin-top: 20px;
  }
</style>

<div id="map-container">
  <iframe
    src="https://map.geo.admin.ch/embed.html?X=187027.34&Y=650640.77&zoom=2&lang=de&topic=ech&bgLayer=ch.swisstopo.pixelkarte-grau&layers=KML%7C%7Chttps:%2F%2Fapi3.geo.admin.ch%2Fexamples%2Fbln-style.kml,KML%7C%7Chttps:%2F%2Fpublic.geo.admin.ch%2FX5d851hOR-uy0T3DQHHfJw,ch.bafu.hydroweb-messstationen_gefahren"
    name="IframeGeoadmin"
    allow="geolocation 'self'"
    style="width: 100%; height: 100%; border: none;">
  </iframe>
</div>

<div class="info">
  <h3>Feature Detection</h3>
  <label>Layer Name: </label><span id="name">{layerId}</span><br/>
  <label>Feature Name/ID: </label><span id="placemark">{featureId}</span><br/>
  <h1>{string1}</h1>
</div>
