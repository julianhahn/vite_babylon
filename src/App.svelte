<script lang="ts">
  import svelteLogo from "./assets/svelte.svg";
  import Counter from "./lib/Counter.svelte";
  import {
    Engine,
    Scene,
    ArcRotateCamera,
    Vector3,
    HemisphericLight,
    Mesh,
    MeshBuilder,
    Animation,
  } from "babylonjs";
  import type { IAnimationKey } from "babylonjs";
  import { onMount } from "svelte";
  let canvas;

  const positions: IAnimationKey[] = [
    { frame: 0, value: new Vector3(-3, 0, 0) },
    { frame: 30, value: new Vector3(0, 0, 0) },
    { frame: 60, value: new Vector3(3, 0, 0) },
    { frame: 90, value: new Vector3(0, 0, 0) },
    { frame: 120, value: new Vector3(-3, 0, 0) },
  ];

  function create_animation(): Animation {
    const fps = 60;
    const moveAnim = new Animation(
      "moveAnim",
      "position",
      fps,
      Animation.ANIMATIONTYPE_FLOAT,
      Animation.ANIMATIONLOOPMODE_CYCLE
    );

    moveAnim.setKeys(positions);
    return moveAnim;
  }

  onMount(() => {
    // create the canvas html element and attach it to the webpage
    canvas.style.width = "100%";
    canvas.style.height = "100%";
    canvas.id = "gameCanvas";

    // initialize babylon scene and engine
    var engine = new Engine(canvas, true);
    var scene = new Scene(engine);

    var camera: ArcRotateCamera = new ArcRotateCamera(
      "Camera",
      Math.PI / 2,
      Math.PI / 2,
      30,
      Vector3.Zero(),
      scene
    );

    camera.attachControl(canvas, true);
    var light1: HemisphericLight = new HemisphericLight(
      "light1",
      new Vector3(1, 1, 0),
      scene
    );
    var sphere: Mesh = MeshBuilder.CreateSphere(
      "sphere",
      { diameter: 1 },
      scene
    );

    sphere.position = new Vector3(0, 0, 0);
    sphere.animations.push(create_animation());
    scene.debugLayer.show();

    // hide/show the Inspector
    window.addEventListener("keydown", (ev) => {
      // Shift+Ctrl+Alt+I
      if (ev.shiftKey && ev.ctrlKey && ev.altKey && ev.keyCode === 73) {
        if (scene.debugLayer.isVisible()) {
          scene.debugLayer.hide();
        } else {
          scene.debugLayer.show();
        }
      }
    });

    // run the main render loop
    engine.runRenderLoop(() => {
      scene.render();
    });
  });
</script>

<main class="main">
  <canvas bind:this={canvas} width={32} height={32} />
</main>

<style>
  .main {
    width: 100%;
    height: 100%;
    background-color: green;
  }
</style>
