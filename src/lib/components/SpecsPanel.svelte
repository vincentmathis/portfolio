<script lang="ts">
let feedValue = 0.055;
let killValue = 0.062;

const defaultFeed = 0.055;
const defaultKill = 0.062;

function seed(): void {
    window.dispatchEvent(new CustomEvent('seed-simulation'));
}

function reset(): void {
    feedValue = defaultFeed;
    killValue = defaultKill;
    window.dispatchEvent(new CustomEvent('feed-change', { detail: feedValue }));
    window.dispatchEvent(new CustomEvent('kill-change', { detail: killValue }));
}

function handleFeedChange(event: Event): void {
    const target = event.target as HTMLInputElement;
    feedValue = parseFloat(target.value);
    window.dispatchEvent(new CustomEvent('feed-change', { detail: feedValue }));
}

function handleKillChange(event: Event): void {
    const target = event.target as HTMLInputElement;
    killValue = parseFloat(target.value);
    window.dispatchEvent(new CustomEvent('kill-change', { detail: killValue }));
}
</script>

<div class="specs">
    <div class="spec-item"><span>MODEL</span><span>G-S RD EQUATION</span></div>
    <div class="spec-item"><span>PRECISION</span><span>32-BIT FLOAT</span></div>
    <div class="spec-item">
        <span>FEED (f)</span>
        <span>{feedValue.toFixed(4)}</span>
    </div>
    <input
        type="range"
        min="0.03"
        max="0.07"
        step="0.0001"
        bind:value={feedValue}
        oninput={handleFeedChange}
    >
    <div class="spec-item" style="margin-top: 10px">
        <span>KILL (k)</span>
        <span>{killValue.toFixed(4)}</span>
    </div>
    <input
        type="range"
        min="0.055"
        max="0.067"
        step="0.0001"
        bind:value={killValue}
        oninput={handleKillChange}
    >
    <button onclick={seed} class="spec-btn">RE-GENERATE SEED</button>
    <button onclick={reset} class="spec-btn">RESET SETTINGS</button>
</div>

<style>
.specs {
    position: absolute;
    bottom: var(--space-m);
    right: var(--space-l);
    background: rgba(255, 255, 255, 0.03);
    backdrop-filter: blur(var(--blur));
    -webkit-backdrop-filter: blur(var(--blur));
    border: 1px solid rgba(255, 255, 255, 0.1);
    padding: var(--space-m);
    border-radius: 8px;
    width: 280px;
    pointer-events: auto;
    box-shadow: 0 40px 100px rgba(0, 0, 0, 0.5);
}

.spec-item {
    display: flex;
    justify-content: space-between;
    font-family: "JetBrains Mono", monospace;
    font-size: 0.8rem;
    margin-bottom: 0.8rem;
    color: var(--text-dim);
}

.spec-item span:last-child {
    color: var(--accent);
}

input[type="range"] {
    width: 100%;
    height: 2px;
    background: rgba(255, 255, 255, 0.1);
    -webkit-appearance: none;
    -moz-appearance: none;
    appearance: none;
    margin-top: 5px;
    outline: none;
}

input[type="range"]::-webkit-slider-thumb {
    -webkit-appearance: none;
    appearance: none;
    width: 12px;
    height: 12px;
    background: var(--accent);
    border-radius: 50%;
    cursor: pointer;
}

input[type="range"]::-moz-range-thumb {
    -moz-appearance: none;
    appearance: none;
    width: 12px;
    height: 12px;
    background: var(--accent);
    border: none;
    border-radius: 50%;
    cursor: pointer;
}

.spec-btn {
    border: 1px solid rgba(255, 255, 255, 0.2);
    background: transparent;
    color: white;
    margin-top: 20px;
    width: 100%;
    padding: 0.5rem 1rem;
    border-radius: 3px;
    font-family: "JetBrains Mono", monospace;
    font-size: 0.8rem;
    text-transform: uppercase;
    letter-spacing: 1px;
    cursor: pointer;
    transition: all 0.3s cubic-bezier(0.16, 1, 0.3, 1);
}

.spec-btn:hover {
    transform: translateY(-2px);
    box-shadow: 0 10px 20px rgba(0, 0, 0, 0.2);
    border-color: var(--accent);
}

@media (max-width: 1150px) {
    .specs {
        position: absolute;
        bottom: var(--space-m);
        left: 0;
        right: 0;
        width: auto;
        max-width: 500px;
        margin: 0 auto;
        border-radius: 8px;
        box-sizing: border-box;
    }
}

@media (max-width: 768px) {
    .specs {
        position: relative;
        bottom: auto;
        left: auto;
        right: auto;
        max-width: 100%;
        margin: 0;
        border-radius: 0;
        border-left: none;
        border-right: none;
    }
}
</style>
