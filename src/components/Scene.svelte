<script>
  import * as THREE from "three";
  import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
  import Stats from "three/examples/jsm/libs/stats.module";
  import { onMount, setContext } from "svelte";

  let canvasElement,
    camera,
    clock,
    scene,
    stats,
    uniforms,
    controls,
    renderer,
    fov = 36,
    width,
    height;

  onMount(() => {
    const ctx = canvasElement.getContext("webgl2");
    console.log(ctx);
    console.log(width);
    console.log(height);
    initScene();
    animate();
  });

  function initScene() {
    camera = new THREE.PerspectiveCamera(fov, width / height, 1, 1000);
    camera.position.z = 196;

    clock = new THREE.Clock();
    scene = new THREE.Scene();

    uniforms = {
      u_time: { type: "f", value: 1.0 },
      colorB: { type: "vec3", value: new THREE.Color(0xfff000) },
      colorA: { type: "vec3", value: new THREE.Color(0xffffff) },
    };

    const ctx = canvasElement.getContext("2d");

    renderer = new THREE.WebGLRenderer({
      ctx,
      antialias: true,
    });

    renderer.setSize(self.innerWidth, self.innerHeight);

    controls = new OrbitControls(camera, renderer.domElement);
    stats = Stats();
    document.body.appendChild(stats.dom);

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
    setContext("scene", scene);
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
    camera.aspect = self.innerWidth / self.innerHeightt;
    camera.updateProjectionMatrix();
    renderer.setSize(self.innerWidth, self.innerHeight);
  }
</script>

<canvas
  id="scene"
  bind:offsetWidth={width}
  bind:offsetHeight={height}
  bind:this={canvasElement}
>
  <slot />
</canvas>

<style lang="scss">
  #scene {
    width: 100vw;
    height: 100vh;
  }
</style>
