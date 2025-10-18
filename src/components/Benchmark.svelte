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
                The study by Smit and others used accelerometers to collect
                movement data from Lily and 27 other cats for 1 week in summer
                and 1 week in winter 2023. They then applied a machine learning
                model to determine whether cats were being active, eating,
                grooming, littering, lying, scratching, sitting or standing.
            </p>
            <p>
                According to the study, cats spend the majority of their time
                (86%) displaying inactive behavious — lying, sitting or
                standing. In fact, those are the top 3 activities across both
                seasons for all cats. However, a few cats spend a notable amount
                of time eating in winter or grooming in summer.
            </p>
        </div>
        <MovementSankey />
    </div>
    <div class="content-container-column">
        <div class="text-wrapper">
            <p>
                On average, the cats in the study spend 37% of their time
                sitting and another 37% lying. Cats like Lily who live together with
                other cats spend slightly less time lying and more time sitting.
                Cats spend another 13% of their day standing, less time on
                grooming (6%), eating (6%), being active (3%), and very
                little time on scratching or littering.
            </p>
        </div>
    </div>
    <Scatter {cats} />
</div>
