<script>
  import { getContext } from "svelte";
  import * as THREE from "three";
  import { onMount } from "svelte";

  onMount(() => {
    const scene = getContext("scene");
    console.log("scene: ", scene);
    draw(scene);
  });

  const draw = (scene) => {
    // note: set up plane mesh and add it to the scene
    const planeGeometry = new THREE.PlaneGeometry(64, 64, 64, 64);
    const planeMaterial = new THREE.MeshNormalMaterial({ wireframe: true });
    const planeMesh = new THREE.Mesh(planeGeometry, planeMaterial);

    // const planeCustomMaterial = new THREE.ShaderMaterial({
    //   // note: this is where the magic happens
    //   uniforms: uniforms,
    //   vertexShader: vertexShader(),
    //   fragmentShader: fragmentShader(),
    //   wireframe: true,
    // });

    // const planeMesh = new THREE.Mesh(planeGeometry, planeMaterial);
    planeMesh.rotation.x = -Math.PI / 2 + Math.PI / 4;
    planeMesh.scale.x = 2;
    planeMesh.scale.y = 2;
    planeMesh.scale.z = 2;
    planeMesh.position.y = 8;
    scene.add(planeMesh);
  };
</script>
