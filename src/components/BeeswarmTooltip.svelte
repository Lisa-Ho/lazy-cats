<script>
    export let hoveredData;
    export let tooltipX;
    export let tooltipY;
    const iconWidth = 11;

    function formatHours(hoursProp) {
        const decimalHours = hoursProp * 24 / 100;
        const hours = Math.floor(decimalHours);
        const minutes = Math.round((decimalHours - hours) * 60);
        return `${hours}:${minutes.toString().padStart(2, "0")}`;
    }

    function formatDogData(dog) {
        const withDog = dog == "Yes" ? "a" : "no"
        return withDog
    }

    function formatArea(area) {
        const article = area == "Urban" ? "an" : "a"
        return `${article} ${area.toLowerCase()}`
    }

</script>

<div
    class="tooltip"
    style="position: absolute; top: {tooltipY}px; left: {tooltipX}px;"
>
    <h3>{hoveredData.cat_name}</h3>
    <div
        style="display: flex; gap:0.3rem; flex-direction: row; align-items: baseline;"
    >
        <p class="byline">{hoveredData.cat_age}</p>
        {#if hoveredData.cat_sex == "Neutered male"}
            <svg
                fill="#000000"
                width={iconWidth}
                height={iconWidth}
                overflow="visible"
                viewBox="0 0 611.998 611.998"
            >
                <g>
                    <path
                        d="M569.569,0H413.755c-23.403,0-42.377,18.973-42.377,42.375s18.973,42.375,42.377,42.375h53.51L348.501,203.513
		c-36.655-24.899-79.878-38.296-125.165-38.296c-59.67,0-115.768,23.237-157.961,65.429c-87.098,87.102-87.098,228.824,0,315.922
		c42.193,42.193,98.291,65.431,157.961,65.431s115.768-23.237,157.961-65.431c76.855-76.855,85.878-196.233,27.101-283.094
		l118.797-118.795v53.51c0,23.403,18.973,42.375,42.375,42.375c23.405,0,42.375-18.973,42.375-42.375V42.375
		C611.945,18.973,592.972,0,569.569,0z M321.369,486.64c-26.186,26.186-61.001,40.607-98.032,40.607s-71.846-14.421-98.032-40.607
		c-54.054-54.056-54.054-142.009,0-196.066c26.186-26.186,61.001-40.605,98.032-40.605s71.848,14.421,98.032,40.605
		C375.422,344.632,375.422,432.587,321.369,486.64z"
                    />
                </g>
            </svg>
        {/if}
        {#if hoveredData.cat_sex == "Neutered female"}
            <svg
                fill="#000000"
                width={iconWidth}
                height={iconWidth}
                overflow="visible"
                viewBox="0 0 40.249 40.249"
            >
                <g>
                    <path
                        d="M26.875,27.999h-4.25v-4.646c5.315-1.15,9.312-5.887,9.312-11.542C31.937,5.298,26.638,0,20.125,0
                    S8.312,5.299,8.312,11.812c0,5.655,3.996,10.391,9.312,11.542v4.646h-4.25c-1.381,0-2.5,1.119-2.5,2.5s1.119,2.5,2.5,2.5h4.25v4.75
                    c0,1.381,1.119,2.5,2.5,2.5c1.381,0,2.5-1.119,2.5-2.5v-4.75h4.25c1.381,0,2.5-1.119,2.5-2.5S28.256,27.999,26.875,27.999z
                     M13.312,11.812C13.312,8.056,16.37,5,20.125,5c3.755,0,6.813,3.057,6.813,6.812c0,3.758-3.058,6.813-6.813,6.813
                    C16.37,18.625,13.312,15.567,13.312,11.812z"
                    />
                </g>
            </svg>
        {/if}
    </div>
    <p style="margin-bottom: 0rem;">
        Sits or lies {formatHours(hoveredData.prop_lazy)} hours a day. Their favourite pose is {hoveredData.pose1} ({formatHours(hoveredData.pose1_value)}). Lives in {formatArea(hoveredData.area)} area with 
        {formatDogData(hoveredData.dog)} dog.
    </p>
</div>

<style>
    .tooltip {
        transform: translateX(-50%);
        text-align: left;
        padding: 10px;
        min-width: 140px;
        max-width: 200px;
        word-wrap: break-word;
        background: white;
        border-radius: 4px;
        border: 0px solid;
        pointer-events: none;
        transition:
            top 200ms ease,
            left 200ms ease;
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.15);
        z-index: 1000;
    }

    .byline {
        font-family: "Open Sans", sans-serif;
        font-weight: 300;
        font-size: 0.75rem;
        line-height: 1rem;
        margin-bottom: 0.75rem;
        color: rgb(22, 22, 22);
    }
</style>
