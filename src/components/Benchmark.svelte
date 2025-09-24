<script>
    export let cats;
    import Scatter from "./Scatter.svelte";

</script>

<div class="section-wrapper">
    <div class="content-container-column">
        <h2>Is Lily just a lazy cat?</h2>
        <p>Actually, Lily is pretty average. Most cats love to ...</p>
    </div>
    <Scatter {cats} />
</div>