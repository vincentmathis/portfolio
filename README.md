# Portfolio

My personal portfolio and landing page built with SvelteKit, Tailwind CSS, and a WebGL Gray-Scott reaction-diffusion simulation background.

**Live:** [vincentmathis.xyz](https://vincentmathis.xyz)

## Tech Stack

- [SvelteKit](https://kit.svelte.dev) + [Svelte 5](https://svelte.dev)
- [Tailwind CSS v4](https://tailwindcss.com)
- WebGL2 reaction-diffusion shader simulation
- [Biome](https://biomejs.dev) for linting/formatting

## Development

```bash
bun install
bun run dev
```

## Building

```bash
bun run build
bun run preview
```

## Docker

```bash
docker build -t portfolio .
docker run -p 3000:3000 portfolio
```

## License

GPL-3.0
