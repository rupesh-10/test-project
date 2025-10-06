<template>
	<div
		class="relative flex items-end mx-auto h-[520px] max-w-sm overflow-hidden rounded-[40px] isolate"
	>
		<img
			:src="property.image"
			class="absolute inset-0 h-full w-full object-cover"
		/>
		<div
			class="pointer-events-none absolute inset-x-0 bottom-0 z-10 h-[80%] backdrop-blur-xl bg-gradient-to-t from-black/30 via-black/35 to-transparent [mask-image:linear-gradient(to_top,black_55%,transparent_86%)] [-webkit-mask-image:linear-gradient(to_top,black_55%,transparent_86%)]"
		></div>

		<div class="inset-x-0 bottom-0 z-20 p-5 text-white">
			<div class="mb-2 flex items-center justify-between">
				<h3 class="text-2xl font-extrabold tracking-tight">
					{{ property.title }}
				</h3>
				<span
					class="rounded-full px-3 py-1 text-sm font-bold text-white/90 bg-black/40"
				>
					${{ property.price }}
				</span>
			</div>

			<p class="mb-4 text-[15px] leading-6 text-white/80 line-clamp-2">
				{{ property.description }}
			</p>

			<div class="mb-5 flex gap-2">
				<span
					class="rounded-full px-3 py-1 text-xs text-white/90 backdrop-blur-md bg-white/15"
				>
					{{ property.rating }}
				</span>
				<span
					class="rounded-full px-3 py-1 text-xs text-white/90 backdrop-blur-md bg-white/15"
				>
					{{ property.duration }}
				</span>
			</div>

			<button
				class="w-full rounded-full bg-white text-black font-semibold py-3 cursor-pointer hover:bg-gray-300 transition"
			>
				Reserve
			</button>
		</div>
	</div>
</template>

<script setup>
defineProps({
	property: { type: Object, required: true },
});
</script>

<style scoped>
.line-clamp-2 {
	display: -webkit-box;
	-webkit-line-clamp: 2;
	line-clamp: 2;
	-webkit-box-orient: vertical;
	overflow: hidden;
}
</style>
