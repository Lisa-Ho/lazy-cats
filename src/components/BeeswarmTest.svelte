<script>
    import CatPlay from "./CatPlay.svelte";
    import CatStand from "./CatStand.svelte";
    import CatSit from "./CatSit.svelte";
    import CatSleep from "./CatSleep.svelte";
    import BeeswarmLegend from "./BeeswarmLegend.svelte";
    export let cats;
    
    import * as d3 from "d3";
    import { forceSimulation, forceX, forceY, forceCollide } from "d3-force";

    const component_map = {
        standing: CatStand,
        sitting: CatSit,
        lying: CatSleep,
    };

    const colr_dict = {
        Indoor: "#BED567",
        "Indoor Outdoor": "#53B8CC",
    };

    let width = 600;
    $: height = width / 1.5;
    const margin = { top: 20, right: 20, left: 20, bottom: 20 };

    $: radius = width * 0.038;
    $: visualRadius = width * 0.00072;
    $: buffer = Math.max(12, width * 0.02) * 1.2; //calculated based on font size in px

    let nodes = [];
    let hoveredData;
    let tooltipX = 0;
    let tooltipY = 0;

    $: innerWidth = width - margin.left - margin.right;
    $: centerLine = height / 2.8 - margin.top;

    $: xScale = d3
        .scaleLinear()
        .domain(d3.extent(cats, (d) => d.prop_lazy))
        .range([margin.left + radius, innerWidth - radius - radius]);

    $: yTarget = (d) =>
        d.lazy_diff > 0 ? centerLine - radius - buffer : centerLine + radius + buffer;

    let simulation;

    $: {
        cats.sort((a, b) => b.lazy_diff - a.lazy_diff);

        if (simulation) simulation.stop();

        cats.forEach((d) => {
            d.x = xScale(d.prop_lazy);
            d.fx = d.x;
        });

        simulation = forceSimulation(cats)
            .force("y", forceY().y(yTarget).strength(0.2))
            .force("collide", forceCollide().radius(radius))
            .on("tick", () => {
                cats.forEach((d) => {
                    if (d.lazy_diff > 0 && d.y > centerLine - radius - buffer)
                        d.y = centerLine - radius - buffer;
                    if (d.lazy_diff < 0 && d.y < centerLine + radius + buffer)
                        d.y = centerLine + radius + buffer;
                });
                nodes = [...cats];
            });
    }

    //get x/y coords for tooltip
    function handleHover(node, event) {
        const container = document.querySelector(".chart-container");
        const containerRect = container.getBoundingClientRect();

        hoveredData = node;
        tooltipX = event.clientX - containerRect.left + 10; // offset for visibility
        tooltipY = event.clientY - containerRect.top + 10;
    }
</script>

<div
    class="chart-container"
    role="button"
    tabindex="0"
    bind:clientWidth={width}
    on:mouseleave={() => {
        hoveredData = null;
    }}
    style="border: 2px solid blue"
    >
    <BeeswarmLegend {colr_dict}/>

    <svg {width} {height} overflow="visible">
        <!---->
        <rect x={0} y={0} {width} {height} fill="none" stroke="red" />

        <!-- Line -->
        <line
            x1={margin.left}
            x2={width - margin.left}
            y1={centerLine + radius}
            y2={centerLine + radius}
            stroke="gray"
            stroke-dasharray="4"
        />

        <!-- Markers -->
        {#each nodes as node, i}
            {#if component_map[node.pose1]}
                <g
                    transform={`translate(${node.x}, ${node.y}) scale(${visualRadius})`}
                >
                    <svelte:component
                        this={component_map[node.pose1]}
                        season={colr_dict[node.housing]}
                        stripe1={node.stripe1}
                        stripe2={node.stripe2}
                        stripe3={node.stripe3}
                        on:mouseover={(e) => handleHover(node, e)}
                        on:focus={(e) => handleHover(node, e)}
                        tabindex="0"
                    />
                </g>
            {/if}
        {/each}
    </svg>
    {#if hoveredData}
        <div
            class="tooltip"
            style="position: absolute; top: {tooltipY}px; left: {tooltipX}px;"
        >
            <h3>{hoveredData.cat_name}</h3>
            <p class="byline">{hoveredData.cat_sex}, {hoveredData.cat_age}</p>
            <p style="margin-bottom: 0rem;">
                Likes {hoveredData.lazy_season}, {hoveredData.housing} and {hoveredData.lazy_diff}
            </p>
        </div>
    {/if}
</div>

<!--
 -->

<style>
    .chart-container {
        position: relative;
        padding-top: 1rem;
    }

    .legend {
        font-size: 1rem;
    }

    .legend-title {
        font-weight: 600;
    }

    .tooltip {
        transform: translateX(-50%);
        text-align: left;
        padding: 8px;
        padding-left: 8px;
        padding-right: 8px;
        min-width: 140px;
        max-width: 180px;
        word-wrap: break-word;
        background: white;
        border-radius: 4px;
        border: 0px solid;
        pointer-events: none;
        transition:
            top 200ms ease,
            left 200ms ease;
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.15);
    }

    h3 {
        font-family: "Inter", sans-serif;
        font-weight: 600;
        font-size: 1.05rem;
        margin-bottom: 0.1rem;
    }

    p {
        font-family: "Inter", sans-serif;
        font-weight: 300;
        font-size: 1rem;
        line-height: 1.65em;
        margin-bottom: 0.5rem;
        color: rgb(22, 22, 22);
    }

    .byline {
        font-family: "Inter", sans-serif;
        font-weight: 300;
        font-size: 0.7rem;
        line-height: 1rem;
        margin-bottom: 0.75rem;
        color: rgb(22, 22, 22);
    }
</style>
