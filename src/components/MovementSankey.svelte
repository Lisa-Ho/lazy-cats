<script>
    import { LayerCake, Svg } from "layercake";
	import Sankey from "./Sankey.svelte";
	import seasonalData from "../assets/sankey-data.json"
    //import data from "../assets/sankey-test.js";

	const selectedSeason = 'winter'; 
	const data = seasonalData[selectedSeason];

    
</script>

<div class="wrapper"> 
<div class="chart-container">
    <LayerCake data={data}>
        <Svg>
            <Sankey
				nodeWidth=15
            />
        </Svg>
    </LayerCake>
</div>
</div>

<style>
    .chart-container {
        width: 100%;
        height: 100%;
		
    }

    .wrapper {
        max-width: 550;
        height: 300px;
		margin-bottom: 2rem;
    }
</style>
