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
    right: 90,
    bottom: 0,
    left: 90,
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
  <svg {width} {height} role="figure" tabindex="0">
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

<details>
  <summary>Background Info</summary>
  <h2 id="what-is-this-">What is this?</h2>
  <p>
    A super simple SVG bar-chart made with Svelte and D3. See the source code <a
      target="_blank"
      rel="noopener noreferrer"
      href="https://github.com/seblammers/a11y-bar-tabindex"
      >on github &nearr;</a
    >.
  </p>

  <h2 id="why-">Why?</h2>
  <p>
    To try to find ways to make these kinds of charts more
    accessibility-friendly (keyboard-navigation, screen-readers).
  </p>

  <h2 id="what-does-the-chart-currently-offer-">
    What does the chart currently offer?
  </h2>
  <ul>
    <li>the chart as a whole can be selected via keyboard</li>
    <li>
      the screen-reader will announce the chart as follows
      <ul>
        <li>
          &quot;Bar-Chart showing the heaviest penguin per species in grams.
          Group.&quot;
        </li>
      </ul>
    </li>
    <li>the three bars inside the chart can be selected as well</li>
    <li>
      these three bars will be announced as follows
      <ul>
        <li>
          &quot;Bar-Chart bars. Selected. Required selection contains 3
          items.&quot;
        </li>
      </ul>
    </li>
    <li>
      each bar will then be announced as follows
      <ul>
        <li>
          &quot;The heaviest penguin of the Adelie species weighed 4,775 grams.
          1 of 3.&quot;
        </li>
        <li>
          &quot;The heaviest penguin of the Chinstrap species weighed 4,800
          grams. 2 of 3.&quot;
        </li>
        <li>
          &quot;The heaviest penguin of the Gentoo species weighed 6,300 grams.
          3 of 3.&quot;
        </li>
      </ul>
    </li>
    <li>
      this offers users the ability to go through the chart step-by-step and go
      back and forth if desired
    </li>
  </ul>

  <h2 id="what-s-the-issue-">What&#39;s the issue?</h2>
  <p>
    Currently, this chart uses <code>tabindex = 0</code> to make individual bars
    inside the chart &quot;tab-able&quot;. This leads to automatic warnings by Svelte
    that say
  </p>

  <blockquote>
    <p>
      &quot;A11y: noninteractive element cannot have nonnegative tabIndex
      value&quot;
    </p>
  </blockquote>
  <p>
    I&#39;m curious to see if there is a better way to <em
      >achieve the same type of enriched keyboard/screen-reader accessibility</em
    >
    while <em>avoiding the a11y warnings</em>.
  </p>

  <h2>Get involved</h2>
  <p>
    Do you know a better way to make this chart keyboard-accessible? Do you have
    any other things to say? Let me know: <a
      target="_blank"
      rel="noopener noreferrer"
      href="https://vis.social/@seblammers"
      >https://vis.social/@seblammers &nearr;</a
    >.
  </p>
</details>

<br />

<style>
  .chart-wrapper {
    position: relative;
    width: 100%;
    max-width: 80ch;
    padding: 15px;
    background: #f0f0f0;
    box-shadow: 2px 2px 6px 0 rgba(0, 0, 0, 0.15);
    border-radius: 3px;
  }
  rect {
    fill: steelblue;
  }

  h1,
  h2 {
    margin-bottom: 0;
  }

  p {
    margin-top: 0;
  }

  blockquote {
    font-style: italic;
  }

  details {
    max-width: 80ch;
    margin: 3rem 0;
  }
</style>
