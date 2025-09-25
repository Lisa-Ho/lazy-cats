<script>
    export let cats;
    import Scatter from "./Scatter.svelte";
    import MovementSankey from "./MovementSankey.svelte";
</script>

<div class="section-wrapper">
    <div class="content-container-column">
        <h2>Is Lily just a lazy cat?</h2>
        <div class="text-wrapper">
            <p>Actually, Lily is pretty average.</p>
            <p>
                The study tracked the movements of Lily and 27 other cats. Most
                cats love to ...
            </p>
        </div>
        <MovementSankey />
    </div>
    <div class="content-container-column">
        <div class="text-wrapper">
            <p>
                Most cats that took part in the study spend the majority of
                their time either sitting or lying. They also tend to stand
                around more than 10% of their day. On average, cats spend more
                time grooming and eating than being active, scratching or
                littering.
            </p>
        </div>
    </div>
    <Scatter {cats} />
</div>
