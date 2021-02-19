<script lang="ts">
  let videoDisplay: HTMLVideoElement;
  let currentScale: number = 1;

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
    videoDisplay.style.transform = `scale(-1,1) scale(${currentScale})`;
  }
</script>

<svelte:head><title>Simple camera preview</title></svelte:head>

<main on:wheel={scroll}>
  <!-- svelte-ignore a11y-media-has-caption -->
  <video bind:this={videoDisplay} src="" />
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
    max-height: calc(100vh - 10px);
    overflow: hidden;
  }

  video {
    display: block;
    transform: scale(-1, 1);
    max-height: 100%;
  }
</style>
