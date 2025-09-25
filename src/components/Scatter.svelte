<script>
    import * as d3 from "d3";
    import { axisTop, select } from "d3";
    export let cats;

    //format data
    const movements = [
        "sitting",
        "lying",
        "standing",
        "grooming",
        "eating",
        "active",
        "scratching",
        "littering",
    ];

    $: formattedData =
        Array.isArray(cats) && cats.length
            ? cats
                  .flatMap((cat) =>
                      movements.map((movement) => ({
                          movement,
                          value: cat[`prop_${movement}`],
                          cat_name: cat.cat_name,
                      })),
                  )
                  .sort((a, b) => {
                      if (a.cat_name === "Lily") return 1;
                      if (b.cat_name === "Lily") return -1;
                      return 0;
                  })
            : [];

    $: movementMedians = movements.map((movement) => {
        const values = cats.map((cat) => cat[`prop_${movement}`]);
        return {
            movement,
            median: d3.median(values),
        };
    });

    $: console.log(movementMedians);

    //settings for chart (axis, size, bars)
    let width = 450;
    let height = 400;
    const margin = { top: 20, right: 40, left: 100, bottom: 20 };
    const axisBuffer = 20;

    $: xScale = d3
        .scaleLinear()
        .domain([0, d3.max(formattedData, (d) => d.value)])
        .range([margin.left, width - margin.right]);

    $: yScale = d3
        .scalePoint()
        .domain(movements)
        .range([margin.top + axisBuffer, height - margin.bottom])
        .padding(0.5);

    let xAxisRef;
    const customTicks = [0, 10, 20, 30, 40, 50, 60];
    $: if (xAxisRef && xScale) {
        const axis = axisTop(xScale)
            .tickValues(customTicks)
            .tickSize(0)
            .tickFormat((d) => formatHours(d));
        select(xAxisRef).call(axis);
    }

    //formatting functions for rendering labels
    function toSentenceCase(text) {
        if (!text) return "";
        return text.charAt(0).toUpperCase() + text.slice(1).toLowerCase();
    }

    function formatHours(hoursProp) {
        return `${Math.round(hoursProp)}%`;
    }
</script>

<div class="chart-container" id="scatter-chart" bind:clientWidth={width}>
    <div class="chart-title">
        <p>Average time cats spend by activity</p>
    </div>
    <div class="legend-container">
        <div class="legend">
            <div class="legend-item">
                <span class="swatch-lily"></span>
                <span class="legend-label">Lily</span>
            </div>
            <div class="legend-item">
                <span class="swatch-cats"></span>
                <span class="legend-label">Other cats</span>
            </div>
            <div class="legend-item">
                <span class="swatch-median"></span>
                <span class="legend-label">Median</span>
            </div>
        </div>
    </div>
    <svg {width} {height}>
        <!-- xAxis -->
        <g
            bind:this={xAxisRef}
            class="x-axis"
            transform={`translate(0, ${margin.top + axisBuffer - 10})`}
        />

        <!-- Gridlines -->
        {#each customTicks as tick}
            <line
                x1={xScale(tick)}
                x2={xScale(tick)}
                y1={margin.top + axisBuffer - 10}
                y2={height - margin.bottom}
                stroke="#cccccc"
                stroke-width="1"
                stroke-dasharray="2,2"
            />
        {/each}

        <!-- Median values -->
        {#each movementMedians as { movement, median }}
            <path
                d={`M ${xScale(median)} ${yScale(movement) + 12}
                    l -4 6
                    l 8 0
                     Z`}
                fill="#111"
                opacity="1"
            />
        {/each}

        <!-- Axis labels-->
        {#each movements as movement}
            <text
                x={margin.left - 20}
                y={yScale(movement)}
                dominant-baseline="middle"
                text-anchor="end"
                class="axis-label"
            >
                {toSentenceCase(movement)}
            </text>
        {/each}

        <!-- Dots -->
        {#each formattedData as { movement, value, cat_name }}
            <circle
                cx={xScale(value)}
                cy={yScale(movement)}
                r="10"
                opacity="0.3"
                fill="#53B8CC"
            />
            <circle
                cx={xScale(value)}
                cy={yScale(movement)}
                r="10"
                opacity="1"
                fill="none"
                stroke={cat_name === "Lily" ? "#222" : "#666"}
                stroke-width={cat_name === "Lily" ? 2 : 0.2}
            />
        {/each}
    </svg>
</div>

<style>
    

    :global(.tick text) {
        font-size: 0.9rem;
        fill: #444444;
        font-family: "Open Sans", sans-serif;
    }

    :global(.tick line) {
        stroke: #888888;
    }

    :global(.x-axis .domain) {
        display: none;
    } 

    .legend {
        display: flex;
        justify-content: left;
        flex-direction: row;
        align-items: left;
        gap: 1.5rem;
    }

    .legend-item {
        display: flex;
        align-items: center;
        gap: 0.3rem;
    }

    .legend-label {
        line-height: 1rem;
        font-size: 0.9rem;
        font-family: "Open Sans", sans-serif;
        margin-top: 0rem;
    }

    .swatch-cats,
    .swatch-lily {
        width: 16px;
        height: 16px;
        border-radius: 50%;
        display: inline-block;
    }

    .swatch-cats {
        background-color: #53b8cc;
        opacity: 0.5;
    }

    .swatch-lily {
        background-color: #fff;
        border: 2px solid #222;
    }

    .swatch-median {
        width: 0;
        height: 0;
        border-left: 8px solid transparent;
        border-right: 8px solid transparent;
        border-bottom: 12px solid #222; /* triangle color */
        vertical-align: middle;
    }
</style>
