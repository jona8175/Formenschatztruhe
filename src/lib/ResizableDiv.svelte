<script>
  let width = 300; // Initial width

  let resizableDiv; // Reference to the resizable div

  function startResize(event) {
    event.preventDefault(); // Prevent default behavior

    window.addEventListener('mousemove', resize);
    window.addEventListener('mouseup', stopResize);
  }

  function resize(event) {
    if (resizableDiv) {
      width = event.clientX - resizableDiv.getBoundingClientRect().left;
    }
  }

  function stopResize() {
    window.removeEventListener('mousemove', resize);
    window.removeEventListener('mouseup', stopResize);
  }
</script>

<style>
  .old_image{
    position: absolute;

  }
  .resizable {
    position: absolute;
    width: var(--width);
    height: auto;
    background-color: lightblue;
    border: 1px solid #ccc;
    overflow: hidden;
  }

  .content {
  }

  .resizer {
    width: 10px;
    height: 100%;
    background: #333;
    position: absolute;
    right: 0;
    top: 0;
    cursor: ew-resize;
  }
</style>

<img class = "old_image" src="Image.png" alt="Italian Trulli">
<div class="resizable" bind:this={resizableDiv} style="--width: {width}px;">
  <div class="content">
      <img class = "cont_img" src="Image.png" alt="Italian Trulli">
  </div>
  <div class="resizer" on:mousedown={startResize}></div>
</div>
