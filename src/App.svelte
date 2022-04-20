<script>
  import Audio from "./components/Audio.svelte";
  import Scene from "./components/Scene.svelte";
  import Mesh from "./components/Mesh.svelte";
</script>

<main>
  <Audio />

  <Scene>
    <Mesh />
  </Scene>
</main>

<style type="scss">
  main {
    height: 100%;
  }
</style>
