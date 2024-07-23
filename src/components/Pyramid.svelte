<script>
    export let value;
    export let xMScale;
    export let xFScale;
    export let yScale;
    export let color;
</script>

{#each value as d}
<rect
  x={d.variable === "Male" ? xMScale(d.number) : xFScale(0)}
  y={yScale(d.AgeGrp)}
  width={d.variable === "Male" ? Math.abs(xMScale(0) - xMScale(d.number)) : Math.abs(xFScale(d.number) - xFScale(0))}
  height={yScale.bandwidth()}
  stroke={color}
  stroke-width = 1.2
  fill="none"
  opacity="0.8"
/>
{/each}