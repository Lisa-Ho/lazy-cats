<script>
    import CatPlay from "./CatPlay.svelte";
    import CatStand from "./CatStand.svelte";
    import CatSit from "./CatSit.svelte";
    import CatSleep from "./CatSleep.svelte";
    import cats from "../assets/cats.json";
    import * as d3 from "d3";

    const colr_dict = {
        "Indoor": "#BED567",
        "Indoor Outdoor": "#53B8CC",
    };

    let width = 400;
    let height = 400; 

    const margin = {top: 20, right: 50, 
        left: 50, bottom:20  }

    const component_map = {
        standing: CatStand,
        sitting: CatSit,
        lying: CatSleep,
    };

    $: xScale = d3.scaleLinear()  
        .domain([d3.min(cats, d => d.prop_lazy), d3.max(cats, d => d.prop_lazy)])
        .range([margin.left, width - margin.left - margin.right])

    const yScale = d3.scaleLinear()
        .domain([d3.min(cats, d => d.lazy_diff), d3.max(cats, d => d.lazy_diff)])
        .range([height - margin.top - margin.bottom, 0])

    console.log(cats[0].prop_lazy)

    let hoveredData;
    let tooltipX = 0;
    let tooltipY = 0;

    function handleHover(cat, event) {
        const container = document.querySelector('.chart-container');
        const containerRect = container.getBoundingClientRect();

        hoveredData = cat;
        tooltipX = event.clientX - containerRect.left + 10 ; // offset for visibility
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
    <svg width={width} height={height} overflow="visible">
        {#each cats as cat}
            {#if component_map[cat.pose1]}
            <g
            transform={`translate(${xScale(cat.prop_lazy)}, ${yScale(cat.lazy_diff)}) scale(${width*0.001})`}
            >
                <svelte:component
                    this={component_map[cat.pose1]}
                    season={colr_dict[cat.housing]}
                    stripe1={cat.stripe1}
                    stripe2={cat.stripe2}
                    stripe3={cat.stripe3}
                    on:mouseover={(e) => handleHover(cat, e)}
                    on:focus={(e) => handleHover(cat, e)}
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
            <p style="margin-bottom: 0rem;">Likes {hoveredData.pose1}, {hoveredData.pose2} and {hoveredData.pose3}</p>
        </div>
    {/if}
</div>


<!--

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

