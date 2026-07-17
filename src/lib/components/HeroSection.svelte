<script lang="ts">
// Hero section logic can be added here
</script>

<div class="hero-card" id="parallaxCard">
    <div class="tagline">// Software Engineer</div>
    <h1>Vincent Mathis</h1>
    <p class="description">
        Building things on the web. Sometimes useful, sometimes just interesting.
    </p>
    <div class="cta-group">
        <a href="/projects" class="btn btn-primary">fun projects</a>
    </div>
</div>

<style>
.hero-card {
    pointer-events: auto;
    background: rgba(255, 255, 255, 0.02);
    backdrop-filter: blur(var(--blur)) saturate(120%);
    -webkit-backdrop-filter: blur(var(--blur)) saturate(120%);

    border: 1px solid rgba(255, 255, 255, 0.1);

    padding: var(--space-l);
    border-radius: 24px;
    max-width: 720px;

    box-shadow: 0 40px 100px rgba(0, 0, 0, 0.5);

    position: relative;
    overflow: hidden;
}

.hero-card::after {
    content: "";
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    pointer-events: none;
    background: linear-gradient(135deg, rgba(255, 255, 255, 0.1) 0%, transparent 40%);
    border-radius: 24px;
}

h1 {
    font-size: clamp(3rem, 8vw, 5rem);
    line-height: 0.95;
    font-weight: 700;
    margin: 0;
    letter-spacing: -0.04em;
    text-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);
}

.tagline {
    font-family: "JetBrains Mono", monospace;
    color: var(--accent);
    font-size: 0.9rem;
    font-weight: 600;
    margin-bottom: var(--unit);
    text-transform: uppercase;
    letter-spacing: 3px;
    text-shadow: 0 0 15px rgba(0, 242, 234, 0.3);
}

.description {
    margin-top: var(--space-m);
    font-size: 1.15rem;
    line-height: 1.6;
    color: #fff;
    max-width: 550px;
    text-shadow: 0 2px 10px rgba(0, 0, 0, 0.8);
}

.cta-group {
    margin-top: var(--space-l);
    display: flex;
    gap: var(--unit);
    pointer-events: auto;
}

.btn {
    padding: 1rem 2rem;
    border-radius: 4px;
    font-size: 0.8rem;
    font-weight: 600;
    text-transform: uppercase;
    letter-spacing: 1px;
    cursor: pointer;
    transition: all 0.3s cubic-bezier(0.16, 1, 0.3, 1);
    text-decoration: none;
    display: inline-block;
}

.btn-primary {
    background: var(--text);
    color: var(--bg);
    border: none;
}

.btn-primary:hover {
    transform: translateY(-2px);
    box-shadow: 0 10px 20px rgba(0, 0, 0, 0.2);
    background: var(--accent);
}

@media (max-width: 1000px) {
    h1 {
        font-size: clamp(1.5rem, 4vw, 2rem);
        line-height: 0.95;
        font-weight: 700;
        margin: 0;
        letter-spacing: -0.04em;
        text-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);
    }
    .hero-card {
        width: 100%;
        max-width: 500px;
        margin: 0 auto;
        text-align: center;
        border-radius: 0px;
    }

    .hero-card::after {
        border-radius: 0px;
    }

    .cta-group {
        justify-content: center;
    }
}
</style>
