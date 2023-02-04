<script>
  // do you know a better way to make this chart keyboard-accessible?
  // let me know: https://vis.social/@seblammers
  import { format } from "d3-format";
  import { scaleLinear, scaleBand } from "d3-scale";
  import { max } from "d3-array";

  let title = "Heavy Penguins";
  let description =
    "Bar-Chart showing the heaviest penguin per species in grams.";

  let data = [
    { species: "Adelie", body_mass_g: 4775 },
    { species: "Chinstrap", body_mass_g: 4800 },
    { species: "Gentoo", body_mass_g: 6300 },
  ];

  const formatLabel = format(",.0f");

  const xAccessor = (d) => +d.body_mass_g;
  const yAccessor = (d) => d.species;

  const margin = {
    top: 0,
    right: 110,
    bottom: 0,
    left: 180,
  };

  let width = 400;
  let height = 500;

  $: innerWidth = width - margin.left - margin.right;
  let innerHeight = height - margin.top - margin.bottom;

  $: xScale = scaleLinear()
    .domain([0, max(data, xAccessor)])
    .range([0, innerWidth]);

  const yScale = scaleBand()
    .domain(data.map((d) => d.species))
    .range([innerHeight, 0])
    .padding(0.25);

  $: xAccessorScaled = (d) => xScale(xAccessor(d));
  $: yAccessorScaled = (d) => yScale(yAccessor(d));
</script>

<svelte:head>a11y bar chart: tabindex</svelte:head>

<div class="chart-wrapper" bind:clientWidth={width}>
  <h1>{title}</h1>
  <svg class="chart" {width} {height} role="figure" tabindex="0">
    <title>{description}</title>
    <g
      transform={`translate(${margin.left}, ${margin.top})`}
      tabindex="0"
      role="list"
      aria-label="bar chart bars"
    >
      {#each data as d}
        <g
          role="listitem"
          tabindex="0"
          aria-label="The heaviest penguin of the {yAccessor(
            d
          )} species weighed {xAccessor(d)} grams."
        >
          <text
            text-anchor="end"
            x={-10}
            y={yAccessorScaled(d) + yScale.bandwidth() / 2}
            dy=".32em"
          >
            {yAccessor(d)}
          </text>
          <rect
            x={0}
            y={yAccessorScaled(d)}
            width={xAccessorScaled(d)}
            height={yScale.bandwidth()}
          />
          <text
            text-anchor="start"
            x={xAccessorScaled(d)}
            dx="10"
            y={yAccessorScaled(d) + yScale.bandwidth() / 2}
            dy=".32em"
          >
            {formatLabel(d.body_mass_g)} g
          </text>
        </g>
      {/each}
    </g>
  </svg>
</div>

<style>
  .chart-wrapper {
    position: relative;
    width: 100%;
    max-width: 60ch;
  }
  rect {
    fill: steelblue;
  }

  h1 {
    margin-bottom: 0;
  }
</style>
