<script>
  import data from "./data/2024_population_final.json";
  import Pyramid from "./components/Pyramid.svelte";
  import { group, max} from "d3-array";
  import { scaleLinear, scaleBand } from "d3-scale";
  import AxisY from "/components/AxisY.svelte";
  import AxisX_F_CN from "/components/AxisX_F_CN.svelte";
  import AxisX_M_CN from "/components/AxisX_M_CN.svelte";
  import AxisX_F_US from "/components/AxisX_F_US.svelte";
  import AxisX_M_US from "/components/AxisX_M_US.svelte";
  import AxisX_F_IN from "/components/AxisX_F_IN.svelte";
  import AxisX_M_IN from "/components/AxisX_M_IN.svelte";
   
   let countryName = "China";
   let initialData = data;
   let renderedData = initialData;

    $: renderedData = initialData.filter(d => d.Country == countryName);
    let ChinaData = initialData.filter(d => d.Country == "China");
    let USData = initialData.filter(d => d.Country == "US");
    let IndiaData = initialData.filter(d => d.Country == "India");

    $:renderedData = group(renderedData, (d) => d.Time);
    
    $:renderedCNData = group(ChinaData, (d) => d.Time);
    $:renderedUSData = group(USData, (d) => d.Time);
    $:renderedINData = group(IndiaData, (d) => d.Time);

    $:console.log(max[0, ChinaData])

    
    const scaleFactor = 3;
    
    let width = 720;
    let height = 720;
    
    const margin = {
        top: 80 / scaleFactor,
        left: 10 / scaleFactor,
        bottom: 90 / scaleFactor,
        right: 10 / scaleFactor,
      };
    
      $: innerWidth = width / scaleFactor - margin.left - margin.right;
      let innerHeight = height / scaleFactor - margin.top - margin.bottom;
    
      $: xMScale_CN = scaleLinear()
            .domain([0, max(ChinaData, d => d.number)]) //extent(data, (d) => d.Index)
            .range([innerWidth / 2, margin.left]);
      
      $: xFScale_CN = scaleLinear()
        .domain(xMScale_CN.domain())
        .range([innerWidth / 2, innerWidth - margin.right])

      $: xMScale_IN = scaleLinear()
            .domain([0, max(IndiaData, d => d.number)]) //extent(data, (d) => d.Index)
            .range([innerWidth / 2, margin.left]);
      
      $: xFScale_IN = scaleLinear()
        .domain(xMScale_IN.domain())
        .range([innerWidth / 2, innerWidth - margin.right])

      $: xMScale_US = scaleLinear()
            .domain([0, max(USData, d => d.number)]) //extent(data, (d) => d.Index)
            .range([innerWidth / 2, margin.left]);
      
      $: xFScale_US = scaleLinear()
        .domain(xMScale_US.domain())
        .range([innerWidth / 2, innerWidth - margin.right])
    
      const yScale = scaleBand()
            .domain(renderedData.map(d => d.AgeGrp))
            .range([innerHeight, 0])
            .padding(0.1);
      
      $: xFTicks_US = xFScale_US.ticks(3);
      $: xMTicks_US = xMScale_US.ticks(3); 
      $: xFTicks_IN = xFScale_IN.ticks(3);
      $: xMTicks_IN = xMScale_IN.ticks(3);
      $: xFTicks_CN = xFScale_CN.ticks(3);
      $: xMTicks_CN = xMScale_CN.ticks(3);         

      $: yTicks = [0, 50, 100];
</script>

    <main bind:clientWidth={width}>
    <div>
    <h1>Projected population pyramids, in million</h1>
    <section>
    {#each [...renderedCNData] as [key, value]}
    <div class="chart-container">
    <h2>{key}</h2>
    <svg width = {width / scaleFactor} height = {height / scaleFactor}>
        <g transform="translate({margin.left} {margin.top})">
          <!-- {#if key==2024 } -->
          <AxisY {yTicks} {yScale} width = {width / scaleFactor}/>
          <AxisX_M_CN {xMScale_CN} {xMTicks_CN} height = {innerHeight} width = {innerWidth}/>
          <AxisX_F_CN {xFScale_CN} {xFTicks_CN} height = {innerHeight} width = {innerWidth}/>
          <!-- {/if} -->
           <Pyramid value={value} 
           color="#9574AF" 
           xMScale = {xMScale_CN}
           xFScale = {xFScale_CN}
           {yScale}/>
        </g>
    </svg>
    </div>
    {/each}
    <h3>China</h3>
    </section>
    <section>
    {#each [...renderedINData] as [key, value]}
    <div class="chart-container">
    <svg width = {width / scaleFactor} height = {height / scaleFactor}>
        <g transform="translate({margin.left} {margin.top})">
          <!-- {#if key==2024 } -->
          <AxisY {yTicks} {yScale} width = {width / scaleFactor}/>
          <AxisX_M_IN {xMScale_IN} {xMTicks_IN} height = {innerHeight} width = {innerWidth}/>
          <AxisX_F_IN {xFScale_IN} {xFTicks_IN} height = {innerHeight} width = {innerWidth}/>
          <!-- {/if} -->
           <Pyramid value={value} 
           color="#5AC5C2" 
           xMScale = {xMScale_IN}
           xFScale = {xFScale_IN}
           {yScale}/>
        </g>
    </svg>
    </div>
    {/each}
    <h3>India</h3>
  </section>
  <section>
    {#each [...renderedUSData] as [key, value]}
    <div class="chart-container">
    <svg width = {width / scaleFactor} height = {height / scaleFactor}>
        <g transform="translate({margin.left} {margin.top})">
          <!-- {#if key==2024 } -->
          <AxisY {yTicks} {yScale} width = {width / scaleFactor}/>
          <AxisX_M_US {xMScale_US} {xMTicks_US} height = {innerHeight} width = {innerWidth}/>
          <AxisX_F_US {xFScale_US} {xFTicks_US} height = {innerHeight} width = {innerWidth}/>
          <!-- {/if} -->
           <Pyramid value={value} 
           color="#EDC639" 
           xMScale = {xMScale_US}
           xFScale = {xFScale_US}
           {yScale}/>
        </g>
    </svg>
    </div>
    {/each}
    <h3>U.S.</h3>
    </section>
  </div>
    </main>
    
    <style>

    :global(.tick text, .axis-title){
      fill: #666;
      text-anchor: start;
      font-family: RetinaNarrowLight,  sans-serif;
      font-size: 13px;
      line-height: 15.6px;
    }

    section{
      margin: 0;
      display: flex;
      place-items: center;
      min-width: 320px;
    }

    main{
    max-width: 800px;
    margin: 0 auto;
    }

    h1{ 
      color: #333333;
      font-family: Retina, sans-serif;
      font-size: 15px;
      font-weight: 500;
      line-height: 20px;
      margin-bottom: 10px;
      margin-left: 30px;
    }

    h2{ 
      color: #333333;
      font-family: Retina, sans-serif;
      font-size: 15px;
      font-weight: 500;
      margin-top: 10px;
      margin-left: 43%;
    }

    h3{ 
      color: #333333;
      font-family: Retina, sans-serif;
      font-size: 15px;
      font-weight: 300;
      margin-bottom: 10px;
      margin-left: 30px;
      text-transform: uppercase;
    }

    </style>