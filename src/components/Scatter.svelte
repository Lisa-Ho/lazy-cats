<script>
    export let cats;

    //format data
    $: formattedData = Array.isArray(cats) && cats.length ? "test" : [] ;

    //settings for chart (axis, size, bars)
    let width = 450;
    const margin = { top: 20, right: 110, left: 85, bottom: 20 };
    
</script>

<div class="chart-container" id="scatter-chart" bind:clientWidth={width}>
    <div class="chart-title">
        <p>Chart title here</p>
    </div>
</div>

<style>
    #scatter-chart {
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
</style>
