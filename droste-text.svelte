<!-- Courtesy of vadymhimself on CodePen: https://codepen.io/vadymhimself/pen/vwgKqO -->
<script lang="ts">
  import { onMount } from "svelte";
  import { tweened, type Tweened } from "svelte/motion";

  const elems = Array.from(Array(10).keys());

  let textNodes: HTMLElement[] = [];
  let halfX = () => window.innerWidth / 2;
  let halfY = () => window.innerHeight / 2;

  type Position = { x: number; y: number; z: number };
  let tweens: Tweened<Position>[] = [];
  let xValues: number[] = [];
  let yValues: number[] = [];
  let zValues: number[] = [];

  onMount(() => {
    textNodes.forEach((_, i) => {
      let tween = tweened({ x: 0, y: 0, z: i + 8 } as Position);
      tweens.push(tween);
      tween.subscribe((value) => {
        xValues[i] = value.x;
        yValues[i] = value.y;
        zValues[i] = value.z;
      });
    });

    const onMouseMove = (e: MouseEvent) => {
      tweens.forEach((tween, i) => {
        tween.update((n) => ({
          ...n,
          x: (e.clientX - halfX()) * (i + 1) * 0.01,
          y: (e.clientY - halfY()) * (i + 1) * 0.01,
        }));
      });
    };

    const onMouseOut = () => {
      tweens.forEach((tween, i) => {
        tween.update((n) => ({
          ...n,
          x: 0,
          y: 0,
        }));
      });
    };

    window.addEventListener("mousemove", onMouseMove);
    window.addEventListener("mouseout", onMouseOut);
  });
</script>

<header class="wrap">
  {#each elems as _, i}
    <span
      class={"text text-8xl"}
      bind:this={textNodes[i]}
      style="--x: {xValues[i]}px; --y: {yValues[i]}px; --z: {zValues[i]}px;"
    >
      Michalis W<span>ö</span>ss
    </span>
  {/each}
</header>

<style>
  .wrap {
    position: relative;
    perspective: 100px;
    text-transform: uppercase;
  }

  .text {
    white-space: nowrap;
    position: absolute;
    top: 0;
    left: 50%; /* Position at 50% from left */
    transform: translateX(-50%) translate3d(var(--x), var(--y), var(--z)); /* Move back by half of its width */
    text-shadow: -1px -1px 0 rgba(240, 240, 240, 1),
      1px -1px 0 rgba(240, 240, 240, 1), -1px 1px 0 rgba(240, 240, 240, 1),
      1px 1px 0 rgba(240, 240, 240, 1);
    transform-style: preserve-3d;
  }
</style>
