<script>
    export let hoveredData;
    export let tooltipX;
    export let tooltipY;
</script>

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

<style>
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
        font-weight: 400;
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
