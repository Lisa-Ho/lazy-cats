<script>
    import Beeswarm from "./components/Beeswarm.svelte";
    import cats from "./assets/cats.json";
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
    
    <section>
        <h2>Is Lily just a really lazy cat?</h2>
        <p>Actually, Lily is pretty average. Most cats love to ...</p>
    </section>
    
    <section>
        <h2>Lying and sitting is most common</h2>
        <p>Most cats prefer to lie or sit, followed by standing</p>
    </section>
    
    <section>
        <h2>Explore lazy cats yourself</h2>
        <p>Check out their favourite poses, weight, age, gender and how they live.</p>
        <Beeswarm {cats}/>
    </section>

    <section class="Footer" style="padding-top:4rem;">
        <p>Made with sweat and love</p>
    </section>
    
</main>



<style>

    main {
        max-width: 1000px;
        margin: 4rem auto 0px auto;
        padding-left: 1rem;
        padding-right: 1rem;   
    }

    section {
        text-align: center;
        padding-bottom: 3rem;
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
        font-weight: 400;
        font-size: 1rem;
        line-height: 1.65em;
        margin-bottom: 0.5rem;
        color: rgb(22, 22, 22);
    }
   

</style>

