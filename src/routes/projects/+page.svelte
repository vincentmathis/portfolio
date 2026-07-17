<script lang="ts">
import { onMount } from 'svelte';

let projects = $state([
    {
        title: 'Life',
        description:
            'A visual journey through your lifespan. See how you spend your time, week by week.',
        href: '#',
        accent: '#0d5c63',
    },
    {
        title: 'ChadGPT',
        description: 'An AI chat with attitude. Powered by Groq, flavored with looksmaxxing.',
        href: '#',
        accent: '#10b981',
    },
]);

onMount(() => {
    const host = window.location.hostname;
    projects[0].href = `https://life.${host}`;
    projects[1].href = `https://chadgpt.${host}`;
});
</script>

<svelte:head>
    <title>Projects | Vincent Mathis</title>
</svelte:head>

<div class="projects-page">
    <nav>
        <a href="/" class="back-link">&larr; Home</a>
    </nav>

    <main>
        <h1>Fun Projects</h1>
        <p class="subtitle">Things I built because I felt like it.</p>

        <div class="grid">
            {#each projects as project}
                <a href={project.href} class="project-card" style="--accent: {project.accent}">
                    <h2>{project.title}</h2>
                    <p>{project.description}</p>
                    <span class="arrow">&rarr;</span>
                </a>
            {/each}
        </div>
    </main>
</div>

<style>
.projects-page {
    min-height: 100vh;
    background: #030305;
    color: #fff;
    font-family: "Inter", sans-serif;
    padding: var(--space-m, 1.6rem) var(--space-l, 2.6rem);
}

nav {
    margin-bottom: 4rem;
}

.back-link {
    color: rgba(255, 255, 255, 0.5);
    text-decoration: none;
    font-family: "JetBrains Mono", monospace;
    font-size: 0.85rem;
    letter-spacing: 1px;
    text-transform: uppercase;
    transition: color 0.2s;
}
.back-link:hover {
    color: var(--accent, #00f2ea);
}

main {
    max-width: 700px;
}

h1 {
    font-size: clamp(2rem, 5vw, 3rem);
    font-weight: 700;
    letter-spacing: -0.03em;
    margin: 0;
}

.subtitle {
    color: rgba(255, 255, 255, 0.5);
    margin-top: 0.5rem;
    font-size: 1.05rem;
}

.grid {
    margin-top: 3rem;
    display: flex;
    flex-direction: column;
    gap: 1rem;
}

.project-card {
    display: block;
    padding: 1.5rem 2rem;
    background: rgba(255, 255, 255, 0.03);
    border: 1px solid rgba(255, 255, 255, 0.08);
    border-radius: 12px;
    text-decoration: none;
    color: inherit;
    transition: all 0.25s ease;
    position: relative;
}
.project-card:hover {
    background: rgba(255, 255, 255, 0.06);
    border-color: var(--accent);
    transform: translateY(-2px);
    box-shadow: 0 8px 30px rgba(0, 0, 0, 0.3);
}

.project-card h2 {
    font-size: 1.3rem;
    font-weight: 600;
    margin: 0;
    color: var(--accent);
}

.project-card p {
    margin: 0.5rem 0 0;
    color: rgba(255, 255, 255, 0.6);
    font-size: 0.95rem;
    line-height: 1.5;
}

.arrow {
    position: absolute;
    right: 1.5rem;
    top: 50%;
    transform: translateY(-50%);
    font-size: 1.2rem;
    color: rgba(255, 255, 255, 0.2);
    transition: all 0.25s ease;
}
.project-card:hover .arrow {
    color: var(--accent);
    transform: translateY(-50%) translateX(4px);
}
</style>
