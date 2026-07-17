<script lang="ts">
// Navigation props and logic can be added here
</script>

<nav>
    <div class="logo">Vincent Mathis // Software Engineer</div>
    <div class="nav-links">
        <a href="/projects">Projects</a>
        <a href="mailto:vincent@vincentmathis.xyz">Contact</a>
        <a href="https://github.com/vincentmathis" target="_blank" rel="noopener">GitHub</a>
    </div>
</nav>

<style>
nav {
    position: relative;
    z-index: 20;
    padding: var(--space-m) var(--space-l);
    display: flex;
    justify-content: space-between;
    align-items: center;
    pointer-events: auto;
}

.logo {
    font-weight: normal;
    letter-spacing: 3px;
    text-transform: uppercase;
    white-space: nowrap;
    font-size: clamp(0.8rem, 2vw, 1.8rem);
    text-shadow: 0 2px 10px rgba(0, 0, 0, 0.5);
}

.nav-links {
    font-weight: bold;
    display: flex;
    gap: var(--space-m);
    font-family: "JetBrains Mono", monospace;
    font-size: clamp(1.5rem, 2vw, 1.8rem);
    text-transform: uppercase;
    letter-spacing: 3px;
}

.nav-links span,
.nav-links a {
    cursor: pointer;
    transition:
        color 0.3s ease,
        transform 0.3s ease;
    text-shadow: 0 0 8px rgba(0, 0, 0, 0.8);
    color: inherit;
    text-decoration: none;
}

.nav-links span:hover,
.nav-links a:hover {
    color: var(--accent);
    transform: scale(110%);
}

@media (max-width: 1300px) {
    nav {
        flex-direction: column;
        gap: 1.2rem;
        padding: 1.5rem 1rem;
    }

    .nav-links {
        gap: 0.8rem;
        flex-wrap: wrap;
        justify-content: center;
    }
    .nav-links a,
    .nav-links span {
        font-size: clamp(0.9rem, 2vw, 1.8rem);
        display: inline-block;
    }
}
</style>
