<script lang="ts">
  let videoDisplay: HTMLVideoElement;
  let videoStyle: string;
  let flipX: boolean = localStorage.getItem('flipX') === 'true';
  let flipY: boolean = localStorage.getItem('flipY') === 'true';
  let currentScale: number = 1;

  $: videoStyle = `
    transform: 
    ${flipX ? 'scale(-1,1)' : ''}
    ${flipY ? 'scale(1,-1)' : ''}
    scale(${currentScale})
  `;

  if (navigator.mediaDevices.getUserMedia) {
    navigator.mediaDevices
      .getUserMedia({
        video: {
          width: { ideal: 1920 },
          height: { ideal: 1080 },
          frameRate: 60,
          noiseSuppression: true,
        },
      })
      .then((stream) => {
        videoDisplay.srcObject = stream;
        videoDisplay.play();
      });
  }

  function scroll(event) {
    currentScale -= event.deltaY * 0.05;
    currentScale = Math.max(1, currentScale);
  }

  function keydown(event: KeyboardEvent) {
    if (event.key === 'v') flipY = !flipY;
    if (event.key === 'h') flipX = !flipX;

    localStorage.setItem('flipX', flipX.toString());
    localStorage.setItem('flipY', flipY.toString());
  }
</script>

<svelte:head><title>Simple camera preview</title></svelte:head>

<main on:wheel={scroll} on:keydown={keydown}>
  <!-- svelte-ignore a11y-media-has-caption -->
  <video style={videoStyle} bind:this={videoDisplay} src="" />
  <div class="info">
    <p>Controls:</p>
    <ul>
      <li>h - flip horizontally</li>
      <li>v - flip vertically</li>
    </ul>
  </div>
</main>

<style>
  * {
    padding: 0;
    margin: 0;
    box-sizing: border-box;
  }
  main {
    display: flex;
    justify-content: center;
    align-items: center;
    max-height: calc(100vh - 15px);
    overflow: hidden;
  }

  video {
    display: block;
    max-height: 100%;
  }

  .info {
    position: absolute;
    left: 30px;
    bottom: 20px;
    text-shadow: black 1px 1px 1px;
    font-weight: bold;
  }
</style>
