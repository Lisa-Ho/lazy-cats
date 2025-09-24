<script>
    import Beeswarm from "./components/Beeswarm.svelte";
    import Hero from "./components/Hero.svelte";
    import Intro from "./components/Intro.svelte";
    import Benchmark from "./components/Benchmark.svelte";
    import cats from "./assets/cats.json";

</script>


<main>
    <section>
        <Hero />
    </section>
    
    <section>
        <Intro {cats} />
    </section>
    
    <section>
        <Benchmark {cats}/>
    </section>
    
    <section>
        <h2>Does this mean all cats are lazy?</h2>
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

</style>

