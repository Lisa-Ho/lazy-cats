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

        const minX = 80; // minimum offset from left edge
        const maxX = containerRect.width - 60; // adjust based on tooltip width

        hoveredData = node;
        const rawX = event.clientX - containerRect.left + 10; // offset for visibility
        tooltipX = Math.max(minX, Math.min(rawX, maxX));
        tooltipY = event.clientY - containerRect.top + 10;
    }

    //x-axis
    const axisTicks = [60, 65, 70, 75, 80, 85];

    function formatHours(hoursProp) {
        return `${Math.round(hoursProp)}%`;
    }
</script>

<div class="section-wrapper" id="beeswarm-section" bind:clientWidth={width}>
    <div class="content-container-column">
        <h2>Explore the cats in the study</h2>
        <div class="text-wrapper">
            <p>
                Check out the 28 cats from the study, their favourite activity,
                weight, age, gender and how they live.
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
            <!-- x-Axis -->
            <line
                x1={margin.left}
                x2={width - margin.left}
                y1={centerLine + radius}
                y2={centerLine + radius}
                stroke="gray"
                stroke-dasharray="4"
            />
            {#each axisTicks as tick}
                <g>
                    <text
                        class="axis-ticklabel"
                        x={xScale(tick)}
                        y={centerLine + radius}
                        >{formatHours(tick)}
                    </text>
                </g>
            {/each}
            <text
                class="axis-title"
                x={xScale(60)}
                y={centerLine + buffer + buffer * 2.3}
                text-anchor="start"
            >
                <tspan x={xScale(59.3)} dy="0em">Time lying</tspan>
                <tspan x={xScale(59.3)} dy="1.2em">or sitting</tspan>
            </text>

            <!-- y-axis -->
            <defs>
                <marker
                    id="arrowhead"
                    markerWidth="6"
                    markerHeight="6"
                    refX="3"
                    refY="3"
                    orient="auto"
                    markerUnits="strokeWidth"
                >
                    <path d="M0,0 L6,3 L0,6 Z" fill="#444" />
                </marker>
            </defs>

            <line
                x1={margin.left + buffer / 2}
                x2={margin.left + buffer / 2}
                y1={centerLine + radius - buffer}
                y2={centerLine + radius - buffer - centerLine / 2}
                stroke="#444"
                stroke-dasharray="0"
                marker-end="url(#arrowhead)"
            />
            <line
                x1={margin.left + buffer / 2}
                x2={margin.left + buffer / 2}
                y1={centerLine + radius + buffer}
                y2={centerLine + radius + buffer + centerLine / 2}
                stroke="#444"
                stroke-dasharray="0"
                marker-end="url(#arrowhead)"
            />
            <text
                class="axis-title"
                x={margin.left + buffer}
                y={centerLine + radius - buffer - centerLine / 2}
                text-anchor="start"
            >
                <tspan x={margin.left} dy="-2.2em">Lazier in</tspan>
                <tspan x={margin.left} dy="1.2em">winter</tspan>
            </text>

            <text
                class="axis-title"
                x={margin.left + buffer}
                y={centerLine + radius + buffer + buffer + centerLine / 2}
                text-anchor="start"
            >
                <tspan x={margin.left} dy="0em">Lazier in</tspan>
                <tspan x={margin.left} dy="1.2em">summer</tspan>
            </text>
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
    #beeswarm-section {
        display: flex;
        flex-direction: column;
        align-items: left;
        position: relative;
        gap: 0rem;
    }

    .axis-title {
        font-size: 0.9rem;
        font-weight: 600;
        fill: #444444;
        font-family: "Open Sans", sans-serif;
        max-width: 10px;
    }

    .axis-ticklabel {
        font-size: 0.9rem;
        font-weight: 400;
        fill: #444444;
        font-family: "Open Sans", sans-serif;
        paint-order: stroke;
        stroke: white;
        stroke-width: 6px;

        dominant-baseline: middle;
    }
</style>
