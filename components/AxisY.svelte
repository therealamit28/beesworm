<script>
  import { transition_in } from "svelte/internal";

    export let yScale;
    const ticks = yScale.domain();
export let groupByContinent;
import {fade} from "svelte/transition";
    
</script>


<g class ="axis y">
    {#each ticks as tick ,index}
    {#if groupByContinent}
<g class='tick' in:fade={{delay:index*100}} out:fade>
    <text y={yScale(tick)} x={0}> {tick}</text>
    </g>
    {/if}
    {/each}
</g>
