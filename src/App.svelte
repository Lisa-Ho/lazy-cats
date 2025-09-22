<script>
    import Beeswarm from "./components/Beeswarm.svelte";
    import Hero from "./components/Hero.svelte";
    import Intro from "./components/Intro.svelte";
    import cats from "./assets/cats.json";
</script>


<main>
    <section class="hero">
        <Hero />
    </section>
    
    <section>
        <Intro />
    </section>
    
    <section>
        <h2>Is Lily just a lazy cat?</h2>
        <p>Actually, Lily is pretty average. Most cats love to ...</p>
    </section>
    
    <section>
        <h2>Are all cats lazy?</h2>
        <p>Most cats prefer to lie or sit, followed by standing</p>
    </section>
    
    <section>
        <h2>Explore all cats</h2>
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
        margin: 2rem auto 0px auto;
        padding-left: 1rem;
        padding-right: 1rem;   
    }

    section {
        text-align: center;
        padding-bottom: 3rem;
    }

    h2 {
        font-family: "Open Sans", sans-serif;
        font-weight: 600;
        font-size: 1.3rem;
        margin-bottom: 1.4rem;
    }

    p {
        font-family: "Open Sans", sans-serif;
        font-weight: 400;
        font-size: 1rem;
        line-height: 1.65em;
        margin-bottom: 0.5rem;
        color: rgb(22, 22, 22);
    }
   

</style>

