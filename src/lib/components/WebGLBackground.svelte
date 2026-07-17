<script lang="ts">
import { onDestroy, onMount } from 'svelte';

let canvas: HTMLCanvasElement;
let gl: WebGL2RenderingContext | null;
let animationFrameId: number;

let feedValue = 0.055;
let killValue = 0.062;

const mouse = { x: 0, y: 0, down: 0 };

const vertexShader = `#version 300 es
		in vec4 position; 
		void main() { 
			gl_Position = position; 
		}`;

const simulationShader = `#version 300 es
		precision highp float;
		uniform sampler2D u_tex;
		uniform vec2 u_res;
		uniform vec2 u_mouse;
		uniform float u_down;
		uniform float u_f;
		uniform float u_k;
		out vec4 fragColor;

		void main() {
			vec2 uv = gl_FragCoord.xy / u_res;
			vec2 p = 1.0 / u_res;
			vec4 center = texture(u_tex, uv);
			vec4 n = texture(u_tex, uv + vec2(0, p.y)) + texture(u_tex, uv - vec2(0, p.y)) +
					 texture(u_tex, uv + vec2(p.x, 0)) + texture(u_tex, uv - vec2(p.x, 0));
			vec4 d = texture(u_tex, uv + p) + texture(u_tex, uv - p) +
					 texture(u_tex, uv + vec2(p.x, -p.y)) + texture(u_tex, uv + vec2(-p.x, p.y));

			vec4 lap = n * 0.2 + d * 0.05 - center;
			float a = center.r; 
			float b = center.g;
			float abb = a * b * b;

			float nextA = a + (1.0 * lap.r - abb + u_f * (1.0 - a));
			float nextB = b + (0.5 * lap.g + abb - (u_k + u_f) * b);

			if (u_down > 0.5 && distance(gl_FragCoord.xy, u_mouse) < 30.0) nextB = 0.8;
			fragColor = vec4(clamp(nextA, 0.0, 1.0), clamp(nextB, 0.0, 1.0), 0.0, 1.0);
		}`;

const renderShader = `#version 300 es
		precision highp float;
		uniform sampler2D u_tex;
		uniform vec2 u_res;
		out vec4 fragColor;
		void main() {
			vec2 uv = gl_FragCoord.xy / u_res;
			float val = 0.0;
			for(int y=-1; y<=1; y++) {
				for(int x=-1; x<=1; x++) {
					val += texture(u_tex, uv + vec2(x,y)/u_res).g;
				}
			}
			val /= 9.0;

			vec3 deep = vec3(0.01, 0.02, 0.04);
			vec3 glow = vec3(0.0, 0.95, 0.9);
			vec3 white = vec3(1.0);

			vec3 col = mix(deep, glow, smoothstep(0.1, 0.4, val));
			col = mix(col, white, smoothstep(0.4, 0.7, val));

			fragColor = vec4(col, 1.0);
		}`;

let simulationProgram: WebGLProgram | null;
let renderProgram: WebGLProgram | null;
let textures: WebGLTexture[] = [];
let framebuffers: WebGLFramebuffer[] = [];
let width = 0;
let height = 0;

function createShader(
    gl: WebGL2RenderingContext,
    type: number,
    source: string
): WebGLShader | null {
    const shader = gl.createShader(type);
    if (!shader) return null;

    gl.shaderSource(shader, source);
    gl.compileShader(shader);

    if (!gl.getShaderParameter(shader, gl.COMPILE_STATUS)) {
        console.error('Shader compilation error:', gl.getShaderInfoLog(shader));
        gl.deleteShader(shader);
        return null;
    }

    return shader;
}

function createProgram(
    gl: WebGL2RenderingContext,
    vertexSource: string,
    fragmentSource: string
): WebGLProgram | null {
    const vertexShader = createShader(gl, gl.VERTEX_SHADER, vertexSource);
    const fragmentShader = createShader(gl, gl.FRAGMENT_SHADER, fragmentSource);

    if (!vertexShader || !fragmentShader) return null;

    const program = gl.createProgram();
    if (!program) return null;

    gl.attachShader(program, vertexShader);
    gl.attachShader(program, fragmentShader);
    gl.linkProgram(program);

    if (!gl.getProgramParameter(program, gl.LINK_STATUS)) {
        console.error('Program linking error:', gl.getProgramInfoLog(program));
        gl.deleteProgram(program);
        return null;
    }

    return program;
}

function initWebGL(): void {
    if (!canvas) return;

    gl = canvas.getContext('webgl2');
    if (!gl) {
        console.error('WebGL2 not supported');
        return;
    }

    gl.getExtension('EXT_color_buffer_float');

    simulationProgram = createProgram(gl, vertexShader, simulationShader);
    renderProgram = createProgram(gl, vertexShader, renderShader);

    if (!simulationProgram || !renderProgram) {
        console.error('Failed to create shader programs');
        return;
    }

    const quadBuffer = gl.createBuffer();
    gl.bindBuffer(gl.ARRAY_BUFFER, quadBuffer);
    gl.bufferData(gl.ARRAY_BUFFER, new Float32Array([-1, -1, 1, -1, -1, 1, 1, 1]), gl.STATIC_DRAW);

    const positionLocation = gl.getAttribLocation(simulationProgram, 'position');
    gl.enableVertexAttribArray(positionLocation);
    gl.vertexAttribPointer(positionLocation, 2, gl.FLOAT, false, 0, 0);

    resizeCanvas();
    initTextures();
    seed();
}

function resizeCanvas(): void {
    if (!canvas) return;

    width = canvas.width = typeof window !== 'undefined' ? window.innerWidth : 800;
    height = canvas.height = typeof window !== 'undefined' ? window.innerHeight : 600;

    if (gl) {
        gl.viewport(0, 0, width, height);
    }

    if (gl && simulationProgram && renderProgram) {
        initTextures();
        seed();
    }
}

function initTextures(): void {
    if (!gl) return;

    textures = [];
    framebuffers = [];

    for (let i = 0; i < 2; i++) {
        const texture = gl.createTexture();
        gl.bindTexture(gl.TEXTURE_2D, texture);
        gl.texImage2D(gl.TEXTURE_2D, 0, gl.RGBA32F, width, height, 0, gl.RGBA, gl.FLOAT, null);
        gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MIN_FILTER, gl.NEAREST);
        gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MAG_FILTER, gl.NEAREST);

        const framebuffer = gl.createFramebuffer();
        gl.bindFramebuffer(gl.FRAMEBUFFER, framebuffer);
        gl.framebufferTexture2D(gl.FRAMEBUFFER, gl.COLOR_ATTACHMENT0, gl.TEXTURE_2D, texture, 0);

        textures.push(texture);
        framebuffers.push(framebuffer);
    }
}

function seed(): void {
    if (!gl) return;

    const data = new Float32Array(width * height * 4);
    for (let i = 0; i < data.length; i += 4) {
        data[i] = 1.0;
    }

    const isMobile = width < 768;
    const yStart = isMobile ? Math.floor(height * 0.3) : 0;
    const yEnd = isMobile ? Math.floor(height * 0.85) : height;

    for (let y = yStart; y < yEnd; y++) {
        for (let x = 0; x < width; x++) {
            const idx = (y * width + x) * 4;
            if (Math.random() > 0.995) data[idx + 1] = 1.0;
        }
    }

    gl.bindTexture(gl.TEXTURE_2D, textures[0]);
    gl.texSubImage2D(gl.TEXTURE_2D, 0, 0, 0, width, height, gl.RGBA, gl.FLOAT, data);
}

function render(): void {
    if (!gl || !simulationProgram || !renderProgram) return;

    gl.useProgram(simulationProgram);

    gl.uniform2f(gl.getUniformLocation(simulationProgram, 'u_res'), width, height);
    gl.uniform2f(gl.getUniformLocation(simulationProgram, 'u_mouse'), mouse.x, mouse.y);
    gl.uniform1f(gl.getUniformLocation(simulationProgram, 'u_down'), mouse.down);
    gl.uniform1f(gl.getUniformLocation(simulationProgram, 'u_f'), feedValue);
    gl.uniform1f(gl.getUniformLocation(simulationProgram, 'u_k'), killValue);

    for (let i = 0; i < 12; i++) {
        gl.bindFramebuffer(gl.FRAMEBUFFER, framebuffers[1]);
        gl.bindTexture(gl.TEXTURE_2D, textures[0]);
        gl.drawArrays(gl.TRIANGLE_STRIP, 0, 4);

        [textures[0], textures[1]] = [textures[1], textures[0]];
        [framebuffers[0], framebuffers[1]] = [framebuffers[1], framebuffers[0]];
    }

    gl.bindFramebuffer(gl.FRAMEBUFFER, null);
    gl.useProgram(renderProgram);
    gl.uniform2f(gl.getUniformLocation(renderProgram, 'u_res'), width, height);
    gl.bindTexture(gl.TEXTURE_2D, textures[0]);
    gl.drawArrays(gl.TRIANGLE_STRIP, 0, 4);

    animationFrameId = requestAnimationFrame(render);
}

function handleMouseMove(event: MouseEvent): void {
    mouse.x = event.clientX;
    mouse.y = height - event.clientY;
}

function handleMouseDown(): void {
    mouse.down = 1;
}

function handleMouseUp(): void {
    mouse.down = 0;
}

function handleTouchStart(event: TouchEvent): void {
    const touch = event.touches[0];
    mouse.x = touch.clientX;
    mouse.y = height - touch.clientY;
    mouse.down = 1;
}

function handleTouchMove(event: TouchEvent): void {
    const touch = event.touches[0];
    mouse.x = touch.clientX;
    mouse.y = height - touch.clientY;
}

function handleTouchEnd(): void {
    mouse.down = 0;
}

function handleFeedChange(event: Event): void {
    feedValue = (event as CustomEvent).detail;
}

function handleKillChange(event: Event): void {
    killValue = (event as CustomEvent).detail;
}

function handleSeedRequest(): void {
    seed();
}

onMount(() => {
    if (!canvas) return;

    initWebGL();
    render();

    window.addEventListener('mousemove', handleMouseMove);
    window.addEventListener('mousedown', handleMouseDown);
    window.addEventListener('mouseup', handleMouseUp);
    window.addEventListener('touchstart', handleTouchStart);
    window.addEventListener('touchmove', handleTouchMove);
    window.addEventListener('touchend', handleTouchEnd);
    window.addEventListener('resize', resizeCanvas);

    window.addEventListener('feed-change', handleFeedChange);
    window.addEventListener('kill-change', handleKillChange);
    window.addEventListener('seed-simulation', handleSeedRequest);
});

onDestroy(() => {
    if (animationFrameId) {
        cancelAnimationFrame(animationFrameId);
    }

    if (typeof window !== 'undefined') {
        window.removeEventListener('mousemove', handleMouseMove);
        window.removeEventListener('mousedown', handleMouseDown);
        window.removeEventListener('mouseup', handleMouseUp);
        window.removeEventListener('touchstart', handleTouchStart);
        window.removeEventListener('touchmove', handleTouchMove);
        window.removeEventListener('touchend', handleTouchEnd);
        window.removeEventListener('resize', resizeCanvas);

        window.removeEventListener('feed-change', handleFeedChange);
        window.removeEventListener('kill-change', handleKillChange);
        window.removeEventListener('seed-simulation', handleSeedRequest);
    }
});
</script>

<canvas bind:this={canvas} id="glCanvas"></canvas>

<style>
#glCanvas {
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    height: 100dvh;
    z-index: 0;
    filter: brightness(0.7);
    touch-action: none;
}
</style>
