<script>
  import { onMount } from "svelte";

  let audioContext, audioElement, analyser, source, dataArray;

  onMount(() => {
    setupAudioContext(audioElement);
  });

  function setupAudioContext() {
    audioContext = new window.AudioContext();
    source = audioContext.createMediaElementSource(audioElement);
    analyser = audioContext.createAnalyser();
    source.connect(analyser);
    analyser.connect(audioContext.destination);
    analyser.fftSize = 1024;
    dataArray = new Uint8Array(analyser.frequencyBitCount);
  }

  async function play() {
    if (audioContext === undefined) {
      setupAudioContext();
    }

    const uniforms = {
      u_time: {
        type: "f",
        value: 1.0,
      },
      u_amplitude: {
        type: "f",
        value: 3.0,
      },
      u_data_arr: {
        type: "float[64]",
        value: dataArray,
      },
      // u_black: { type: "vec3", value: new THREE.Color(0x000000) },
      // u_white: { type: "vec3", value: new THREE.Color(0xffffff) },
    };
  }
</script>

<div class="audio">
  <audio
    src="./hold-on.flac"
    class="panel"
    controls
    autoPlay
    onPlay={play}
    bind:this={audioElement}
  />
</div>

<style type="scss">
  .audio {
    position: absolute;
    width: 100vw;
    height: 100vh;
    top: 0;
    display: grid;
    align-items: center;
    pointer-events: none;
  }

  .panel {
    position: absolute;
    bottom: 5%;
    left: 50%;
    transform: translateX(-50%);
    z-index: 1000;
  }
</style>
