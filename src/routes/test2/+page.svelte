<!--
<script>
    window.addEventListener('message', function(e) {
    // Different message types might come in the future.
    // It's best practice to filter by message type. As
    // can be seen below
    if (e.data.type == 'gaFeatureSelection') {
      document.getElementById('name').innerHTML = e.data.payload['layerId'];
      document.getElementById('placemark').innerHTML = e.data.payload['featureId'];
   }
    
  }, false);


</script>

<style>
    iframe {
    height: 350px;
    width: 100%;
    }

label {
  font-weight: bold;
  font-style: italic;
}

span {
  margin-left: 10px;
}
</style>

<nav>
  <br>
  <h3>Feature Detection</h3>
  <iframe src="https://map.geo.admin.ch/#/map?lang=en&center=2696090.32,1227316.9&z=2.392&bgLayer=ch.swisstopo.pixelkarte-farbe&topic=ech&layers=ch.swisstopo.zeitreihen@year=1864,f;ch.bfs.gebaeude_wohnungs_register,f;ch.bav.haltestellen-oev,f;ch.swisstopo.swisstlm3d-wanderwege,f;ch.vbs.schiessanzeigen,f;ch.astra.wanderland-sperrungen_umleitungen,f;KML%7Chttps://public.geo.admin.ch/api/kml/files/oS6hlAZuT4q5OwD5OGoVLw&featureInfo=default" name="IframeGeoadmin" allow="geolocation 'self'"></iframe>



  <label>Layer Name: </label>
  <span id="name"></span>
  <label>Feature Name/ID: </label>
  <span id="placemark"></span>

</nav>

-->


