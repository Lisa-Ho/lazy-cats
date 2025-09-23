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
    const barHeight = 35;
    const barGap = 12;
    $: maxValue = Math.max(...sortedData.map((d) => d.value));

    function toSentenceCase(text) {
        if (!text) return "";
        return text.charAt(0).toUpperCase() + text.slice(1).toLowerCase();
    }

    function formatHours(hoursProp) {
        const decimalHours = (hoursProp * 24) / 100;
        const hours = Math.floor(decimalHours);
        const minutes = Math.round((decimalHours - hours) * 60);
        return `${hours}:${minutes.toString().padStart(2, "0")}`;
    }
</script>

<div class="chart-container" id="bar-chart">
    <div class="radio-group">
        {#each ["average", "summer", "winter"] as s}
            <label class="radio-option">
                <input
                    type="radio"
                    name="season"
                    value={s}
                    bind:group={season}
                />
                {toSentenceCase(s)}
            </label>
        {/each}
    </div>

    <svg width={chartWidth} height={sortedData.length * barHeight + barGap}>
        {#each sortedData as { pose, value }, i}
            <g transform={`translate(0, ${i * barHeight})`}>
                <text x="0" y={barHeight / 2} class="axis-label"
                    >{toSentenceCase(pose)}</text
                >
                <rect
                    x="80"
                    y="5"
                    height={barHeight - barGap}
                    width={(value / maxValue) * (chartWidth - 120)}
                    fill="#53B8CC"
                />
                <text
                    x={(value / maxValue) * (chartWidth - 120) + 85}
                    y={barHeight / 2}
                    class="value-label"
                >
                    {formatHours(value)}
                </text>
            </g>
        {/each}
    </svg>
</div>

<!-- border: 1px solid #333; -->

<style>
    .radio-group {
        display: flex;
        gap: 1rem;
        margin-bottom: 1rem;
    }

    input[type="radio"] {
        margin-right: 0.3rem;
        opacity: 0;
        cursor: pointer;
    }

    label {
        cursor: pointer;
        font-weight: 400;
        font-size: 1rem;
        font-family: "Open Sans", sans-serif;
        dominant-baseline: middle;
        fill: #222222;
    }

    .radio-option {
        position: relative;
        padding-left: 28px;
        cursor: pointer;
        user-select: none;
        display: inline-block;
    }

    .radio-option input[type="radio"] {
        position: absolute;
        opacity: 0;
        cursor: pointer;
    }

    .radio-option::before {
        content: "";
        position: absolute;
        left: 0;
        top: 4px;
        width: 18px;
        height: 18px;
        border-radius: 50%;
        border: 2px solid #8e8e8e;
        background-color: white;
    }

    .radio-option:has(input[type="radio"]:checked)::before {
        border-color: #ce3bc4; 
    }

    .radio-option:has(input[type="radio"]:checked)::after {
        content: "";
        position: absolute;
        left: 5px;
        top: 9px;
        width: 8px;
        height: 8px;
        border-radius: 50%;
        background-color: #ce3bc4;
    }

    #bar-chart {
        height: 200px;
        width: 100%;
        min-width: 300px;
        align-items: center;
        display: flex;
        flex-direction: column;
        gap: 1rem;
    }

    .axis-label {
        font-size: 1rem;
        font-family: "Open Sans", sans-serif;
        font-weight: 400;
        dominant-baseline: middle;
        fill: #222222;
    }

    .value-label {
        font-size: 1rem;
        font-family: "Open Sans", sans-serif;
        font-weight: 400;
        dominant-baseline: middle;
        fill: #222222;
    }
</style>
