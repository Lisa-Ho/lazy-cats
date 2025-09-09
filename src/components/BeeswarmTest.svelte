<script>
    import CatPlay from "./CatPlay.svelte";
    import CatStand from "./CatStand.svelte";
    import CatSit from "./CatSit.svelte";
    import CatSleep from "./CatSleep.svelte";
    import { onMount } from "svelte";
    import cats from "../assets/cats.json";
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
    let height = 500;

    const margin = { top: 40, right: 50, left: 50, bottom: 40 };

    const RADIUS = 40;
    let nodes = [];
    let centerLine;
    let hoveredData;
    let tooltipX = 0;
    let tooltipY = 0;

    onMount(() => {
        const innerWidth = width - margin.left - margin.right;

        // X scale
        const xScale = d3
            .scaleLinear()
            .domain(d3.extent(cats, (d) => d.prop_lazy))
            .range([0, innerWidth]);

        // Lock x position
        cats.forEach((d) => {
            d.x = xScale(d.prop_lazy);
            d.fx = d.x;
        });

        const buffer = RADIUS;
        centerLine = height / 2 - margin.top;
        const minY = centerLine - RADIUS;
        const maxY = centerLine + RADIUS;

        const yTarget = (d) =>
            d.lazy_diff > 0 ? centerLine - buffer : centerLine + buffer;

        const simulation = forceSimulation(cats)
            .force("y", forceY().y(yTarget).strength(0.2))
            .force("collide", forceCollide().radius(RADIUS))
            .on("tick", () => {
                nodes = [...simulation.nodes()];
            });

        simulation.on("tick", () => {
            cats.forEach((d) => {
                if (d.lazy_diff > 0 && d.y > minY) d.y = minY;
                if (d.lazy_diff < 0 && d.y < maxY) d.y = maxY;
            });
            nodes = [...cats];
        });
    });

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
>
    <svg {width} {height} overflow="visible">
        <line
            x1="0"
            x2={width}
            y1={height / 2}
            y2={height / 2}
            stroke="gray"
            stroke-dasharray="4"
        />
        {#each nodes as node, i}
            {#if component_map[node.pose1]}
                <g
                    transform={`translate(${node.x}, ${node.y}) scale(${width * 0.0008})`}
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
                Likes {hoveredData.lazy_season}, {hoveredData.pose2} and {hoveredData.pose3}
            </p>
        </div>
    {/if}
</div>

<!--


<div class="chart-container" bind:clientWidth={width}>
    <svg {width} {height}  overflow="visible">
        {#each nodes as node, i}
            <circle 
                cx={node.x} cy={node.y} r={RADIUS} 
                fill={colr_dict[node.housing]} />
        {/each}
    </svg>
</div>


 -->

<style>
    .chart-container {
        position: relative;
        padding: 2rem 0.2rem 2rem 0.2rem;
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
