<script>
    import Beeswarm from "./components/BeeswarmTest.svelte";
</script>


<main>
    <section class="hero">
    <h1>Lazy cats</h1>
    <p>Test for how to display custom svgs and add tooltips to them.</p>
    </section>
    
    <section class="intro">
    <h2>Lily</h2>
    <p>This is Lily. Lily loves lying around around. She does so around 12 hours a day</p>
    </section>
    
    <section class="beeswarm">
        <h2>Is Lily just a really lazy cat?</h2>
        <p>Actually, Lily is pretty average. Most cats love to ...</p>
    </section>
    
    <section class="sankey">
        <h2>Lying and sitting is most common</h2>
        <p>Most cats prefer to lie or sit, followed by standing</p>
    </section>
    
    
    <section class="interactive">
        <h2>Explore lazy cats yourself</h2>
        <p>Cats by lazy hours, housing setting, and weight</p>
        <Beeswarm/>
    </section>

    <section class="Footer" style="padding-top:100px;">
        <p>Made with sweat and love</p>
    </section>
    
</main>



<style>

    main {
        max-width: 1000px;
        margin: 20px auto 0px auto;   
    }

    section {
        text-align: center;
        padding-bottom: 4rem;
    }

    .tooltip {
        transform: translateX(-50%);
        padding: 8px;
        padding-left: 8px;
        padding-right: 8px;
        min-width: 140px;
        max-width: 160px;
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

    h1 {
        font-family: "Inter", sans-serif;
        font-weight: 700;
        font-size: 2.4rem;
        margin-bottom: 2rem;
    }

    h2 {
        font-family: "Inter", sans-serif;
        font-weight: 600;
        font-size: 1.5rem;
        margin-bottom: 1.4rem;
    }

    p {
        font-family: "Inter", sans-serif;
        font-weight: 300;
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

