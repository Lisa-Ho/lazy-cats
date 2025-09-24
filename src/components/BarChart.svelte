<script>
    import * as d3 from "d3";
    export let cats = [];

    let season = "average";

    // filter for Lily and reformat data
    const poses = ["sitting", "lying", "standing", "other"];
    $: cat =
        Array.isArray(cats) && cats.length
            ? poses.map((pose) => {
                  const baseKey =
                      pose === "other" ? "pose_other_value" : `prop_${pose}`;
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
        .sort((a, b) => {
            if (a.pose === "other") return 1;
            if (b.pose === "other") return -1;
            return b.value - a.value;
        });

    //settings for chart (axis, size, bars)
    let width = 450;
    const margin = { top: 20, right: 110, left: 85, bottom: 20 };
    const barHeight = 35;
    const barGap = 12;

    $: maxValue = cat.length
        ? Math.max(
              ...cat.flatMap((d) => [
                  d.average ?? 0,
                  d.summer ?? 0,
                  d.winter ?? 0,
              ]),
          )
        : 0;

    $: console.log(maxValue);

    $: xScale = d3
        .scaleLinear()
        .domain([0, maxValue])
        .range([0, width - margin.right - margin.left]);

    //formatting functions for rendering labels
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

    $: sittingLyingSum = cat.length
        ? (cat.find((d) => d.pose === "sitting")?.[season] ?? 0) +
          (cat.find((d) => d.pose === "lying")?.[season] ?? 0)
        : 0;
</script>

<div class="chart-container" id="bar-chart" bind:clientWidth={width}>
    <div class="chart-title">
        <p>How Lily spends her hours in a day</p>
    </div>

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

    <svg {width} height={sortedData.length * barHeight + barGap + margin.top}>
        <g
            transform={`translate(${width - margin.left + 20}, ${barGap}) scale(0.7)`}
        >
            <path
                d="M2.25195 0.00497898C7.59454 0.536293 11.3217 1.07752 13.4688 1.48642C13.6754 1.52578 14.1858 1.58731 14.667 1.86533C15.1884 2.16663 15.605 2.65833 15.9473 3.38584C16.5991 4.77154 17.1105 7.30667 17.4531 12.0685C18.1428 21.6539 18.142 40.7666 17.665 50.7511C17.4273 55.7285 17.068 58.3916 16.6367 59.8624C16.4196 60.6029 16.1562 61.144 15.7959 61.5265C15.407 61.9392 14.988 62.0695 14.791 62.1378C14.4357 62.261 14.03 62.4213 13.4473 62.5538C12.8631 62.6866 12.1139 62.7912 10.9834 62.8614C8.74781 63.0003 4.90312 63.0001 1 63.0001C0.447716 63.0001 7.78224e-07 62.5524 0 62.0001C0 61.4478 0.447715 61.0001 1 61.0001C4.93709 61.0001 8.70214 60.9993 10.8594 60.8653C11.9252 60.7991 12.5597 60.7036 13.0039 60.6026C13.449 60.5014 13.7161 60.3946 14.1357 60.2491C14.3264 60.183 14.3185 60.1791 14.3408 60.1554C14.3918 60.1012 14.5409 59.9032 14.7178 59.2999C15.0749 58.0819 15.4292 55.6343 15.667 50.6554C16.1413 40.7266 16.1404 21.6971 15.458 12.212C15.1146 7.43941 14.6137 5.24804 14.1377 4.23642C13.9162 3.76587 13.7401 3.64059 13.666 3.59775C13.5517 3.53169 13.4627 3.52136 13.0947 3.45127C11.0275 3.05756 7.36408 2.52332 2.05371 1.99521C1.50439 1.94042 1.10373 1.45082 1.1582 0.901463C1.21286 0.35189 1.70238 -0.0496755 2.25195 0.00497898Z"
                fill="black"
            />
            <text x="25" y={barHeight} class="annotation">{formatHours(sittingLyingSum)}
 </text>
        </g>

        {#each sortedData as { pose, value }, i}
            <g transform={`translate(0, ${i * barHeight})`}>
                <text x="10" y={barHeight / 2} class="axis-label"
                    >{toSentenceCase(pose)}</text
                >

                <rect
                    x={margin.left}
                    y="5"
                    height={barHeight - barGap}
                    width={xScale(value)}
                    fill="#53B8CC"
                />
                <text
                    x={xScale(value) + margin.left + 5}
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
    #bar-chart {
        height: 250px;
        width: 100%;
        min-width: 320px;
        max-width: 500px;
        align-items: center;
        display: flex;
        flex-direction: column;
        gap: 1rem;
        margin-bottom: 1rem;
    }

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
        min-width: 94px;
        text-align: left;
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

    .radio-option:has(input[type="radio"]:checked) {
        font-weight: 600;
    }

    .radio-option:has(input[type="radio"]:checked)::before {
        border-color: #d63c7c;
    }

    .radio-option:has(input[type="radio"]:checked)::after {
        content: "";
        position: absolute;
        left: 5px;
        top: 9px;
        width: 8px;
        height: 8px;
        border-radius: 50%;
        background-color: #d63c7c;
    }
</style>
