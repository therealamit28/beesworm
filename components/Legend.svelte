<script>
    export let colorScale;
  
    const ticks=colorScale.domain();
    export let hoveredContinent;
    
</script>

<div class ="legend" 
on:mouseleave={()=> {
    hoveredContinent=null;
}}>
    
{#each ticks as continent }

<p
  on:mouseover={()=> {
    hoveredContinent=continent;
  
}}
class:unhovered={hoveredContinent && hoveredContinent !==continent}
>

   
    <span style="background-color: {colorScale(continent)};"></span>
    {continent}
</p>
{/each}
</div>

<style>
    .legend {
        display: flex;
        flex-direction: row;
        justify-content: center;
        flex-wrap:wrap ;
        column-gap: 10px;
        row-gap: 5px; 
        margin-bottom: 5px;
    
    }  
    span{
        width: 9px;
        height: 9px;
        display: inline-block;
        border-radius: 50%;
        border:1px solid rgba(0,0,0,0.5);
    }

    p{
        font-size:14px;
        color: rgba(0,0,0,0.8);
        text-transform: uppercase;
        display: flex;
        align-items: center;
        column-gap: 3px;
        transition: opacity 300ms ease;
        cursor:pointer;
    }
    .unhovered{
        opacity:0.3
    }
</style>