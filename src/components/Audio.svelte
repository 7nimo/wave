<script>
  import * as THREE from "three";
  import { onMount } from "svelte";
  import { scene } from "./Scene.svelte";
  import { vertexShader, fragmentShader } from "../shaders";

  let audioContext, audioElement, analyser, source, dataArray;

  onMount(() => {
    setupAudioContext(audioElement);
    console.log(analyser);
  });

  function setupAudioContext() {
    audioContext = new window.AudioContext();
    // source = audioContext.createMediaElementSource(audioElement);

    // test
    const source = audioContext.createOscillator();
    const g = audioContext.createGain();
    source.connect(g);
    source.type = "sine";
    source.frequency.value = "20.0";
    g.connect(audioContext.destination);
    source.start(0);

    //

    analyser = audioContext.createAnalyser();
    source.connect(analyser);
    analyser.connect(audioContext.destination);
    analyser.fftSize = 2048;
    dataArray = new Float32Array(analyser.frequencyBinCount);
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
      u_black: { type: "vec3", value: new THREE.Color(0x000000) },
      u_white: { type: "vec3", value: new THREE.Color(0xffffff) },
    };

    // note: uncomment these geometries to see different visualizations
    // const planeGeometry = new THREE.BoxGeometry(64, 64, 8, 64, 64, 8);
    // const planeGeometry = new THREE.SphereGeometry(32, 64, 64);

    // note: set up plane mesh and add it to the scene
    const planeGeometry = new THREE.PlaneGeometry(64, 64, 64, 64);
    // const planeMaterial = new THREE.MeshNormalMaterial({ wireframe: true });
    // const planeMesh = new THREE.Mesh(planeGeometry, planeMaterial);
    const planeCustomMaterial = new THREE.ShaderMaterial({
      // note: this is where the magic happens
      uniforms: uniforms,
      vertexShader: vertexShader(),
      fragmentShader: fragmentShader(),
      wireframe: true,
    });

    const planeMesh = new THREE.Mesh(planeGeometry, planeCustomMaterial);
    planeMesh.rotation.x = -Math.PI / 2 + Math.PI / 4;
    planeMesh.scale.x = 2;
    planeMesh.scale.y = 2;
    planeMesh.scale.z = 2;
    planeMesh.position.y = 8;
    scene.add(planeMesh);

    const render = (time) => {
      // note: update audio data
      // analyser.getByteFrequencyData(dataArray);
      analyser.getFloatFrequencyData(dataArray);

      // note: update uniforms
      uniforms.u_time.value = time;
      uniforms.u_data_arr.value = dataArray;

      console.log(dataArray);

      // note: call render function on every animation frame
      requestAnimationFrame(render);
    };

    render();
  }
</script>

<div class="audio">
  <!-- src="./desire.mp3" -->
  <audio
    autoPlay
    class="panel"
    controls
    on:play={play}
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
  }

  .panel {
    position: absolute;
    bottom: 5%;
    left: 50%;
    transform: translateX(-50%);
    z-index: 1000;
  }
</style>
