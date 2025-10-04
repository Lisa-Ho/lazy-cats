<script>
    import Beeswarm from "./components/Beeswarm.svelte";
    import Hero from "./components/Hero.svelte";
    import Intro from "./components/Intro.svelte";
    import Benchmark from "./components/Benchmark.svelte";
    import Conclusion from "./components/Conclusion.svelte";
    import Footer from "./components/Footer.svelte";
    import cats from "./assets/cats.json";

    import { onMount } from "svelte";

    function sendHeight() {
        const height = document.body.scrollHeight;
        window.parent.postMessage({ height }, "https://inside-numbers.com");
    }

    onMount(() => {
        sendHeight();
        window.addEventListener("resize", sendHeight);
        const observer = new MutationObserver(sendHeight);
        observer.observe(document.body, { childList: true, subtree: true });

        return () => {
            window.removeEventListener("resize", sendHeight);
            observer.disconnect();
        };
    });
</script>

<main>
    <section>
        <Hero />
    </section>

    <section>
        <Intro {cats} />
    </section>

    <section>
        <Benchmark {cats} />
    </section>

    <section>
        <Conclusion />
    </section>

    <section>
        <Beeswarm {cats} />
    </section>

    <section>
        <Footer />
    </section>
</main>

<style>
</style>
