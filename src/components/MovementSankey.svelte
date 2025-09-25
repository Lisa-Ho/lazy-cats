<script>
    import { LayerCake, Svg } from "layercake";
    import Sankey from "./Sankey.svelte";
    import seasonalData from "../assets/sankey-data.json";

    let sankeySeason = "average";
    $: sankeyData = seasonalData[sankeySeason];

    const nodeColors = {
        Sitting: "#53B8CC",
        Lying: "#BDD568",
        Standing: "#F58255",
        Sitting2: "#53B8CC",
        Lying2: "#BDD568",
        Standing2: "#F58255",
        Eating2: "#D63C7C",
        Sitting3: "#53B8CC",
        Lying3: "#BDD568",
        Standing3: "#F58255",
        Eating3: "#D63C7C",
        Grooming3: "#888888",
    };

    let width = 500;

    //formatting functions for rendering labels
    function toSentenceCase(text) {
        if (!text) return "";
        return text.charAt(0).toUpperCase() + text.slice(1).toLowerCase();
    }
</script>

<div class="chart-container" id="sankey-chart" bind:clientWidth={width}>
    <div class="chart-title">
        <p>Top three activities of cats by time spent</p>
    </div>
    <div class="radio-group">
        {#each ["average", "summer", "winter"] as s}
            <label class="radio-option">
                <input
                    type="radio"
                    name="sankey-season"
                    value={s}
                    bind:group={sankeySeason}
                />
                {toSentenceCase(s)}
            </label>
        {/each}
    </div>

    <div class="sankey-container">
        <LayerCake data={sankeyData}>
            <Svg>
                <g class="axis-labels">
                    <text x={0} y={-10} text-anchor="start">First</text>
                    <text x={width / 2} y={-10} text-anchor="middle"
                        >Second</text
                    >
                    <text x={width} y={-10} text-anchor="end">Third</text>
                </g>
                <Sankey nodeWidth="20" colorNodes={(d) => nodeColors[d.id]} />
            </Svg>
        </LayerCake>
    </div>
</div>

<style>
    
</style>
