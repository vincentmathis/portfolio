<script lang="ts">
import HeroSection from '$lib/components/HeroSection.svelte';
import Navigation from '$lib/components/Navigation.svelte';
import SpecsPanel from '$lib/components/SpecsPanel.svelte';
import WebGLBackground from '$lib/components/WebGLBackground.svelte';
</script>

<svelte:head>
    <title>Vincent Mathis | Software Engineer</title>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
</svelte:head>

<WebGLBackground />

<div class="page-wrapper">
    <Navigation />

    <main>
        <HeroSection />
    </main>

    <SpecsPanel />
</div>

<style>
:root {
    --bg: #030305;
    --accent: #00f2ea;
    --text: #ffffff;
    --text-dim: rgba(255, 255, 255, 0.9);
    --unit: 1rem;
    --space-m: calc(var(--unit) * 1.618);
    --space-l: calc(var(--unit) * 2.618);
    --blur: 10px;
}

:global(body),
:global(html) {
    margin: 0;
    padding: 0;
    width: 100%;
    height: 100%;
    background: var(--bg);
    color: var(--text);
    font-family: "Inter", sans-serif;
    overflow-x: hidden;
    -webkit-font-smoothing: antialiased;
}

.page-wrapper {
    position: relative;
    z-index: 10;
    min-height: 100vh;
    min-height: 100dvh;
    display: flex;
    flex-direction: column;
    pointer-events: none;
}

.page-wrapper::before {
    content: "";
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    height: 35vh;
    background: linear-gradient(
        to bottom,
        rgba(3, 3, 5, 0.95) 0%,
        rgba(3, 3, 5, 0.8) 15%,
        rgba(3, 3, 5, 0.4) 50%,
        transparent 100%
    );
    z-index: 5;
    pointer-events: none;
}

main {
    flex: 1;
    padding: 0 var(--space-l);
    display: flex;
    flex-direction: column;
    justify-content: center;
    max-width: 800px;
}

@media (max-width: 1000px) {
    :global(body),
    :global(html) {
        overflow-y: auto;
        height: auto;
    }

    .page-wrapper {
        height: auto;
        min-height: 100vh;
        min-height: 100dvh;
        display: block;
    }

    main {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: flex-start;
        max-width: 100%;
        width: 100%;
        box-sizing: border-box;
        padding-bottom: 2rem;
    }
}
</style>
