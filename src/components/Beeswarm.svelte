<script>
    import CatStand from "./CatStand.svelte";
    import CatSit from "./CatSit.svelte";
    import CatSleep from "./CatSleep.svelte";
    import BeeswarmLegend from "./BeeswarmLegend.svelte";
    import BeeswarmTooltip from "./BeeswarmTooltip.svelte";
    export let cats;

    import * as d3 from "d3";
    import { forceSimulation, forceY, forceCollide } from "d3-force";

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
    const margin = { top: 20, right: 5, left: 5, bottom: 20 };

    // responsive height and scaling factors for mobile (<400)
    $: height = width < 400 ? width * 1.5 : width / 1.5;
    $: radius = width < 400 ? width * 0.07 : width * 0.04;
    $: visualRadius = width < 400 ? width * 0.0012 : width * 0.0008;

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
        d.lazy_diff > 0
            ? centerLine - radius - buffer
            : centerLine + radius + buffer;

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
        const container = document.querySelector("#beeswarm");
        const containerRect = container.getBoundingClientRect();

        hoveredData = node;
        tooltipX = event.clientX - containerRect.left + 10; // offset for visibility
        tooltipY = event.clientY - containerRect.top + 10;
    }
</script>

<div class="section-wrapper" id="beeswarm-section" bind:clientWidth={width}>
    <div class="content-container-column">
        <h2>Explore the cats in the study</h2>
        <div class="text-wrapper">
            <p>
                Check out the 28 cats from the study, their favourite activity, weight, age, gender and how
                they live.
            </p>
        </div>
        <BeeswarmLegend {colr_dict} />
    </div>
    
    <div
        class="chart-container"
        id="beeswarm"
        role="button"
        tabindex="0"
        on:mouseleave={() => {
            hoveredData = null;
        }}
    >
        

        <svg {width} {height} overflow="visible">
            <!--
        <rect x={0} y={0} {width} {height} fill="none" stroke="red" />
-->
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
            <BeeswarmTooltip {tooltipY} {tooltipX} {hoveredData} />
        {/if}
    </div>
</div>

<!--
 -->

<style>
    .chart-container {
        position: relative;
    }

    #beeswarm-section {
        display: flex;
        flex-direction: column;
        align-items: left;
        position: relative;
        gap: 2rem;
    }
</style>
