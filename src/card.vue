<template>
	<div
		ref = 'card'
		class = 'holo-card'
		@pointermove = 'move'
		@pointerleave = 'reset'
		@pointercancel = 'reset'
		@pointerup = 'release'
	>
		<div class = 'holo-card__surface'>
			<img :src = 'src' :alt = 'alt' draggable = 'false' @load = 'loaded = true' @error = 'loaded = false' />
			<div v-if = 'loaded && !disabled' class = 'holo-card__foil' aria-hidden = 'true'></div>
			<div v-if = 'loaded && !disabled' class = 'holo-card__glare' aria-hidden = 'true'></div>
		</div>
	</div>
</template>
<script setup lang = 'ts'>
	import { onMounted, onUnmounted, ref, useTemplateRef, watch } from 'vue';

	const props = withDefaults(defineProps<{
		src : string;
		alt ?: string;
		disabled ?: boolean;
		maxTilt ?: number;
	}>(), {
		alt : '',
		disabled : false,
		maxTilt : 12
	});

	const card = useTemplateRef<HTMLDivElement>('card');
	const loaded = ref(false);
	const current = { x : 50, y : 50, rx : 0, ry : 0, opacity : 0 };
	const target = { ...current };
	let frame = 0;
	let lastTime = 0;
	let motion : MediaQueryList | undefined;

	function draw () {
		const style = card.value?.style;
		if (!style) return;
		style.setProperty('--pointer-x', `${current.x}%`);
		style.setProperty('--pointer-y', `${current.y}%`);
		style.setProperty('--rotate-x', `${current.rx}deg`);
		style.setProperty('--rotate-y', `${current.ry}deg`);
		style.setProperty('--shine-opacity', `${current.opacity}`);
	}

	function animate (time : number) {
		const delta = lastTime ? Math.min(time - lastTime, 64) : 16;
		lastTime = time;
		const ease = 1 - Math.exp(-delta / 85);
		let moving = false;
		for (const key of Object.keys(current) as Array<keyof typeof current>) {
			const difference = target[key] - current[key];
			if (Math.abs(difference) > 0.001) {
				current[key] += difference * ease;
				moving = true;
			} else {
				current[key] = target[key];
			}
		}
		draw();
		frame = moving ? requestAnimationFrame(animate) : 0;
		if (!moving) lastTime = 0;
	}

	function schedule () {
		if (!frame) frame = requestAnimationFrame(animate);
	}

	function reset () {
		Object.assign(target, { x : 50, y : 50, rx : 0, ry : 0, opacity : 0 });
		if (props.disabled || motion?.matches) {
			cancelAnimationFrame(frame);
			frame = 0;
			lastTime = 0;
			Object.assign(current, target);
			draw();
		} else {
			schedule();
		}
	}

	function move (event : PointerEvent) {
		if (props.disabled || !loaded.value || motion?.matches) return;
		const rect = card.value?.getBoundingClientRect();
		if (!rect?.width || !rect.height) return;
		const clamp = (value : number) => Math.max(0, Math.min(1, value));
		const x = clamp((event.clientX - rect.left) / rect.width);
		const y = clamp((event.clientY - rect.top) / rect.height);
		const tilt = Number.isFinite(props.maxTilt) ? Math.max(0, Math.min(30, props.maxTilt)) : 12;
		Object.assign(target, {
			x : x * 100,
			y : y * 100,
			rx : (0.5 - y) * tilt * 2,
			ry : (x - 0.5) * tilt * 2,
			opacity : 1
		});
		schedule();
	}

	function release (event : PointerEvent) {
		if (event.pointerType !== 'mouse') reset();
	}

	watch(() => props.src, () => {
		loaded.value = false;
		reset();
	});
	watch(() => props.disabled, reset);

	onMounted(() => {
		motion = window.matchMedia('(prefers-reduced-motion: reduce)');
		motion.addEventListener('change', reset);
	});
	onUnmounted(() => {
		cancelAnimationFrame(frame);
		motion?.removeEventListener('change', reset);
	});
</script>
<style scoped lang = 'scss'>
	.holo-card {
		--pointer-x: 50%;
		--pointer-y: 50%;
		--rotate-x: 0deg;
		--rotate-y: 0deg;
		--shine-opacity: 0;
		width: 280px;
		max-width: 100%;
		perspective: 1000px;
		user-select: none;

		&__surface {
			position: relative;
			width: 100%;
			isolation: isolate;
			overflow: hidden;
			border-radius: 3.5% / 2.5%;
			transform: rotateX(var(--rotate-x)) rotateY(var(--rotate-y));
			box-shadow: 0 8px 24px rgb(0 0 0 / 35%);
			pointer-events: none;
			> img {
				display: block;
				width: 100%;
				height: auto;
			}
		}

		&__foil,
		&__glare {
			position: absolute;
			inset: 0;
			pointer-events: none;
		}

		&__foil {
			background-image: repeating-linear-gradient(
				115deg,
				#ff80ab 0%, #ffe680 12%, #80ffdb 24%,
				#80b3ff 36%, #cf80ff 48%, #ff80ab 60%
			);
			background-size: 300% 300%;
			background-position: var(--pointer-x) var(--pointer-y);
			mix-blend-mode: color-dodge;
			opacity: calc(var(--shine-opacity) * 0.38);
			&::after {
				content: '';
				position: absolute;
				inset: 0;
				background-image: radial-gradient(circle, #fff9 0.5px, transparent 1px);
				background-size: 5px 7px;
				background-position: var(--pointer-x) var(--pointer-y);
				mix-blend-mode: overlay;
				opacity: 0.3;
			}
		}

		&__glare {
			background: radial-gradient(
				circle at var(--pointer-x) var(--pointer-y),
				rgb(255 255 255 / 65%), rgb(255 255 255 / 15%) 25%, transparent 65%
			);
			mix-blend-mode: screen;
			opacity: calc(var(--shine-opacity) * 0.65);
		}

		@media (prefers-reduced-motion: reduce) {
			&__surface {
				transform: none;
			}
			&__foil,
			&__glare {
				display: none;
			}
		}
	}
</style>
