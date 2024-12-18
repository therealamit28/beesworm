<script>
  import data from "./data/data";
  import { forceSimulation, forceX, forceY, forceCollide } from "d3-force";
  import { scaleLinear, scaleBand, scaleOrdinal, scaleSqrt } from "d3-scale";
  import AxisX from "./components/AxisX.svelte";
  import Tooltip from "./components/Tooltip.svelte";
import AxisY from "./components/AxisY.svelte";
import Legend from "./components/Legend.svelte";

 
  let width = 400,
    height = 400;
    let hovered =null;
   
  const margin = { top: 0, right: 0, left: 0, bottom: 25 };

  $: innerWidth = width - margin.left - margin.right;
  let innerHeight = height - margin.bottom - margin.top;

  import { mean, rollups } from "d3-array";
  import{fade} from "svelte/transition";

  
  const continents = rollups(
    data,
    v => mean(v, d => d.happiness),
    d => d.continent
  ) // Group data by continent and return the group-wide mean
    .sort((a, b) => a[1] - b[1]) // Sort according to value
    .map(d => d[0]); // Grab the continent name

  const colorRange = [
    "#dda0dd",
    "#fe7f2d",
    "#fcca46",
    "#a1c181",
    "#619b8a",
    "#eae2b7"
  ];

  let colorScale = scaleOrdinal()
    .domain(continents) 
    .range(colorRange);

  $: radiusScale = scaleSqrt()
    .domain([1, 9])
    .range(width < 568 ? [2, 6] : [3, 8]);

  $: xScale = scaleLinear()
    .domain([1, 9]) 
    .range([0, innerWidth]);

  let yScale = scaleBand()
    .domain(continents)
    .range([innerHeight, 0])
    .paddingOuter(0.5);

  let simulation = forceSimulation(data);
  let nodes = [];
  simulation.on("tick", () => {
    nodes = simulation.nodes();
  });

  $: {
    simulation
      .force(
        "x",
        forceX()
          .x(d => xScale(d.happiness))
          .strength(0.8)
      )
      .force(
        "y",
        forceY()
          .y(d => (groupByContinent? yScale(d.continent):innerHeight/2))
          .strength(0.2)
      )
      .force("collide", forceCollide().radius(d => radiusScale(d.happiness)))
      .alpha(0.3) 
      .alphaDecay(0.0005)
      .restart();
  }
  let hoveredContinent;

  
   let groupByContinent=false;


 
   
</script>
<h1>The Happiest Countries in the World</h1>
<Legend {colorScale} bind:hoveredContinent/>
<div class='chart-container' bind:clientWidth={width} 
on:click={()=>{
  groupByContinent =!groupByContinent;
  hovered=null;
  
}}>

  <svg {width} {height}
  on:mouseleave={()=>{hovered=null;

  }}>
    <g class="inner-chart" transform="translate({margin.left}, {margin.top})" 
   
    >
<AxisX {xScale} height={innerHeight} width={innerWidth}/>
<AxisY {yScale}  {groupByContinent} />



{#if hovered}
<line 
transition:fade
x1={hovered.x}
x2={hovered.x}
y1={innerHeight}
y2={hovered.y}
stroke={colorScale(hovered.continent)}
stroke-width="2"

/>
{/if}

 {#each nodes as node}
        <!-- svelte-ignore a11y-no-noninteractive-tabindex -->
        <circle 
        in:fade={{delay:500}}
        cx={node.x} 
        cy={node.y} 
        r={radiusScale(node.happiness)} 
        fill={colorScale(node.continent)} 
        stroke={
          hovered || hoveredContinent ? hovered===node ||hoveredContinent === node.continent ? "black" : "transparent": "#00000090"
        }
        opacity={
          hovered || hoveredContinent ? (hovered===node || hoveredContinent ===node.continent ? 1:0.3) :1
        }
        on:mouseover={() => hovered = node}
        on:focus={() => hovered = node}
        tabindex="0"
        on:click= {(e)=>{e.stopPropagation();

        }}

         />
      {/each}
    </g>
  </svg>
{
  #if hovered 
} <Tooltip data={hovered} colorScale={colorScale} width={width}/>
{/if}
</div>

<style>
:global(.tick text ,.axis-title){
  font-size: 12px;
font-weight: 400;
fill:hsla(212,10%,53%, 1);
user-select :none;
}
h1{
  font-size: 24px;
  margin-bottom: 6px;
  text-align: center;
  font-weight: 600;
}
circle{
  transition:stroke 300ms ease,opacity 300ms ease;
  cursor:pointer;
}

</style>