<script>

    export let data;
    export let colorScale;
    console.log({ data});
    export let width;
   
    import { fly,fade}  from "svelte/transition";

    let tooltipWidth;
    const xNudge=5;
    const yNudge=5;

    $: xPosition =data.x +tooltipWidth +xNudge >
     width? data.x-tooltipWidth -xNudge
      :data.x +xNudge;
      $: yPosition =data.y+ yNudge ;

</script>

<div 
class="tooltip"
in:fly={{y:10,duration:200,delay:200}}
out:fade
style="position:absolute;
top:{yPosition }px;
left:{xPosition}px;" 
bind:clientWidth={tooltipWidth}
>
<h1>
    {data.country}
</h1>
<div class="info">
    <span class="score">{data.happiness}/10</span>
<span class="continent" style="background:{colorScale(data.continent)}">{data.continent}</span>
</div>
<span class="bar background"/>
<span class="bar foreground" style="background :{colorScale(data.continent)}; width:{data.happiness*10}%;"/>

</div>


<style>
    .tooltip{
        background-color: white;
        box-shadow: 2px 3px 8px rgba(0,0,0,0.15);
        padding:8px 6px;
        border-radius: 3px;
        transition: top 300ms ease, left 300ms ease;
        pointer-events:none;
    }
    .info{
        display: flex;
        justify-content: space-between;
        column-gap: 8px;
        align-items: center;
    }
    .continent{
        font-size: 11px;
        padding: 3px;
        border-radius: 3px;
        text-transform: uppercase;
    }
    h1{
        font-size: 17px;
        margin-bottom: 4px;
        font-weight: 500;
    }
    .score{
        font-size: 12px;
    }
    .bar{
        position :absolute;
        bottom:0;
        left:0;
        height:3px;
        width:100%;
    }
    .bar.background{
        background:#eee
    }
</style>