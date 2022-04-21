<script context="module">
  import * as THREE from "three";

  export let scene = new THREE.Scene();
</script>

<script>
  import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
  import Stats from "three/examples/jsm/libs/stats.module";
  import { onMount } from "svelte";

  let canvas,
    camera,
    clock,
    stats,
    uniforms,
    controls,
    renderer,
    fov = 36,
    width,
    height;

  onMount(() => {
    init();
    animate();
  });

  function init() {
    camera = new THREE.PerspectiveCamera(fov, width / height, 1, 1000);
    camera.position.z = 196;

    clock = new THREE.Clock();

    uniforms = {
      u_time: { type: "f", value: 1.0 },
      colorB: { type: "vec3", value: new THREE.Color(0xfff000) },
      colorA: { type: "vec3", value: new THREE.Color(0xffffff) },
    };

    renderer = new THREE.WebGLRenderer({
      canvas,
      antialias: true,
    });
    renderer.setSize(width, height);

    // ambient light which is for the whole scene
    let ambientLight = new THREE.AmbientLight(0xffffff, 0.7);
    ambientLight.castShadow = false;
    scene.add(ambientLight);

    // spot light which is illuminating the chart directly
    let spotLight = new THREE.SpotLight(0xffffff, 0.55);
    spotLight.castShadow = true;
    spotLight.position.set(0, 80, 10);
    scene.add(spotLight);

    window.addEventListener("resize", () => onWindowResize(), false);

    controls = new OrbitControls(camera, renderer.domElement);
    stats = Stats();
    document.body.appendChild(stats.dom);
  }

  function animate() {
    window.requestAnimationFrame(animate);
    render();
    stats.update();
    controls.update();
  }

  function render() {
    uniforms.u_time.value += clock.getDelta();
    renderer.render(scene, camera);
  }

  function onWindowResize() {
    camera.aspect = width / height;
    camera.updateProjectionMatrix();
    renderer.setSize(width, height);
  }
</script>

<canvas
  id="scene"
  bind:this={canvas}
  bind:clientWidth={width}
  bind:clientHeight={height}
>
  <slot />
</canvas>

<style lang="scss">
  canvas {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
  }
</style>
