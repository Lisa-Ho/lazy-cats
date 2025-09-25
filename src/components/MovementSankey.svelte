<script>
    import { LayerCake, Svg } from "layercake";
	import Sankey from "./Sankey.svelte";
	import seasonalData from "../assets/sankey-data.json"

	const selectedSeason = 'summer'; 
	const data = seasonalData[selectedSeason];

    const nodeColors = {
        "Sitting" : "#53B8CC",
        "Lying" : "#BDD568",
        "Standing" : "#F58255" ,
        "Sitting2" : "#53B8CC",
        "Lying2" : "#BDD568",
        "Standing2" : "#F58255" ,
        "Eating2" : "#D63C7C",
        "Sitting3" : "#53B8CC",
        "Lying3" : "#BDD568",
        "Standing3" : "#F58255" ,
        "Eating3" : "#D63C7C",
        "Grooming3" : "#888888",
    }

</script>

<div class="wrapper"> 
<div class="chart-container">
    <LayerCake data={data}>
        <Svg>   
            <Sankey
				nodeWidth=20
                colorNodes={d => nodeColors[d.id]}
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
