<script>
    export let cats = [];
    let season = "average"; // toggle state

    // filter for Lily and reformat data
    const poses = ["sitting", "lying", "standing"];
    $: cat =
        Array.isArray(cats) && cats.length
            ? poses.map((pose) => {
                  const baseKey = `prop_${pose}`;
                  const cat = cats.filter((d) => d.cat_name === "Lily")[0];
                  return {
                      pose,
                      average: cat[baseKey] ?? null,
                      summer: cat[`${baseKey}_summer`] ?? null,
                      winter: cat[`${baseKey}_winter`] ?? null,
                  };
              })
            : [];

    $: sortedData = cat
        .map((d) => ({ pose: d.pose, value: d[season] }))
        .sort((a, b) => b.value - a.value);

    $: console.log(sortedData);

    const chartWidth = 350;
    const barHeight = 30;
    $: maxValue = Math.max(...sortedData.map((d) => d.value));
</script>

<div class="chart-container" id="bar-chart">
    <div class="toggle">
        {#each ["average", "summer", "winter"] as s}
            <button class:active={season === s} on:click={() => (season = s)}>
                {s}
            </button>
        {/each}
    </div>
    <svg width={chartWidth} height={sortedData.length * barHeight + 10}>
        {#each sortedData as { pose, value }, i}
            <g transform={`translate(0, ${i * barHeight})`}>
                <text x="0" y={barHeight / 2} class="label">{pose}</text>
                <rect
                    x="80"
                    y="5"
                    height={barHeight - 10}
                    width={(value / maxValue) * (chartWidth - 100)}
                    fill="#53B8CC"
                />
                <text
                    x={(value / maxValue) * (chartWidth - 100) + 85}
                    y={barHeight / 2}
                    class="label"
                >
                    {value.toFixed(1)}
                </text>
            </g>
        {/each}
    </svg>
</div>

<!-- border: 1px solid #333; -->

<style>
    #bar-chart {
        height: 200px;
        width: 100%;
        min-width: 300px;
        align-items: center;
        display: flex;
        flex-direction: column;
        gap: 1rem;
    }

    .toggle {
        margin-bottom: 1rem;
    }
    .toggle button {
        margin-right: 0.5rem;
        padding: 0.3rem 0.6rem;
        border: none;
        background-color: #dfe6e9;
        cursor: pointer;
    }
    .toggle button.active {
        background-color: #6c5ce7;
        color: white;
    }
    .label {
        font-size: 14px;
        dominant-baseline: middle;
        fill: #2d3436;
    }
</style>
