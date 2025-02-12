<template>
	<div class="min-h-screen bg-gray-100 py-8 px-4 sm:px-6 lg:px-8">
		<div
			class="max-w-4xl mx-auto bg-white rounded-lg shadow-md overflow-hidden"
		>
			<div class="p-6">
				<h1 class="text-2xl font-bold text-gray-900 mb-6">
					Image Filter Tool
				</h1>

				<div class="mb-6">
					<label
						for="imageUpload"
						class="block text-sm font-medium text-gray-700 mb-2"
					>
						Upload an image
					</label>
					<input
						type="file"
						id="imageUpload"
						accept="image/*"
						@change="handleImageUpload"
						class="block w-full text-sm text-gray-500 file:mr-4 file:py-2 file:px-4 file:rounded-full file:border-0 file:text-sm file:font-semibold file:bg-blue-50 file:text-blue-700 hover:file:bg-blue-100"
					/>
				</div>

				<div v-if="imageUrl" class="space-y-6">
					<div class="grid grid-cols-1 sm:grid-cols-2 gap-6">
						<div>
							<h2
								class="text-lg font-semibold text-gray-900 mb-4"
							>
								Original Image
							</h2>
							<img
								:src="imageUrl"
								alt="Original"
								class="w-full h-auto rounded-lg shadow-md"
							/>
						</div>
						<div>
							<h2
								class="text-lg font-semibold text-gray-900 mb-4"
							>
								Filtered Image
							</h2>
							<img
								:src="imageUrl"
								:style="{ filter: filterStyle }"
								alt="Filtered"
								class="w-full h-auto rounded-lg shadow-md"
							/>
						</div>
					</div>

					<div>
						<h3 class="text-lg font-semibold text-gray-900 mb-4">
							Preset Filters
						</h3>
						<div
							class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 gap-4"
						>
							<button
								v-for="preset in presetFilters"
								:key="preset.name"
								@click="applyPreset(preset)"
								class="px-4 py-2 bg-gray-200 rounded-md text-sm font-medium text-gray-700 hover:bg-gray-300 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-gray-500"
							>
								{{ preset.name }}
							</button>
						</div>
					</div>

					<div class="space-y-4">
						<div v-for="filter in filters" :key="filter.name">
							<label
								:for="filter.name"
								class="block text-sm font-medium text-gray-700 mb-1"
							>
								{{ filter.label }} ({{ filter.value
								}}{{ filter.unit }})
							</label>
							<input
								type="range"
								:id="filter.name"
								v-model="filter.value"
								:min="filter.min"
								:max="filter.max"
								:step="filter.step"
								class="w-full"
							/>
						</div>
					</div>

					<div class="space-y-4">
						<div>
							<h3
								class="text-lg font-semibold text-gray-900 mb-2"
							>
								CSS Filter Code
							</h3>
							<div class="bg-gray-100 p-4 rounded-md">
								<pre class="text-sm text-gray-800">{{
									cssCode
								}}</pre>
							</div>
							<button
								@click="copyCssCode"
								class="mt-2 inline-flex items-center px-4 py-2 border border-transparent text-sm font-medium rounded-md shadow-sm text-white bg-blue-600 hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-blue-500"
							>
								<ClipboardIcon class="h-5 w-5 mr-2" />
								Copy CSS
							</button>
						</div>

						<button
							@click="downloadImage"
							class="inline-flex items-center px-4 py-2 border border-transparent text-sm font-medium rounded-md shadow-sm text-white bg-green-600 hover:bg-green-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-green-500"
						>
							<DownloadIcon class="h-5 w-5 mr-2" />
							Download Filtered Image
						</button>
					</div>
				</div>
			</div>
		</div>
	</div>
</template>

<script setup>
import { ref, computed } from "vue";
import { ClipboardIcon, DownloadIcon } from "lucide-vue-next";

const imageUrl = ref(null);
const filters = ref([
	{
		name: "hue-rotate",
		label: "Hue",
		value: 0,
		min: 0,
		max: 360,
		step: 1,
		unit: "deg",
	},
	{
		name: "saturate",
		label: "Saturation",
		value: 100,
		min: 0,
		max: 200,
		step: 1,
		unit: "%",
	},
	{
		name: "brightness",
		label: "Brightness",
		value: 100,
		min: 0,
		max: 200,
		step: 1,
		unit: "%",
	},
	{
		name: "contrast",
		label: "Contrast",
		value: 100,
		min: 0,
		max: 200,
		step: 1,
		unit: "%",
	},
	{
		name: "grayscale",
		label: "Grayscale",
		value: 0,
		min: 0,
		max: 100,
		step: 1,
		unit: "%",
	},
	{
		name: "sepia",
		label: "Sepia",
		value: 0,
		min: 0,
		max: 100,
		step: 1,
		unit: "%",
	},
	{
		name: "blur",
		label: "Blur",
		value: 0,
		min: 0,
		max: 10,
		step: 0.1,
		unit: "px",
	},
]);

const presetFilters = [
	{
		name: "Normal",
		values: {
			"hue-rotate": 0,
			saturate: 100,
			brightness: 100,
			contrast: 100,
			grayscale: 0,
			sepia: 0,
			blur: 0,
		},
	},
	{
		name: "Vintage",
		values: {
			"hue-rotate": 0,
			saturate: 120,
			brightness: 105,
			contrast: 85,
			grayscale: 10,
			sepia: 30,
			blur: 0,
		},
	},
	{
		name: "Black & White",
		values: {
			"hue-rotate": 0,
			saturate: 0,
			brightness: 100,
			contrast: 120,
			grayscale: 100,
			sepia: 0,
			blur: 0,
		},
	},
	{
		name: "Warm",
		values: {
			"hue-rotate": 30,
			saturate: 130,
			brightness: 105,
			contrast: 110,
			grayscale: 0,
			sepia: 20,
			blur: 0,
		},
	},
	{
		name: "Cool",
		values: {
			"hue-rotate": 180,
			saturate: 110,
			brightness: 100,
			contrast: 105,
			grayscale: 0,
			sepia: 0,
			blur: 0,
		},
	},
	{
		name: "High Contrast",
		values: {
			"hue-rotate": 0,
			saturate: 130,
			brightness: 110,
			contrast: 150,
			grayscale: 0,
			sepia: 0,
			blur: 0,
		},
	},
	{
		name: "Soft Focus",
		values: {
			"hue-rotate": 0,
			saturate: 90,
			brightness: 110,
			contrast: 90,
			grayscale: 0,
			sepia: 10,
			blur: 2,
		},
	},
	{
		name: "Dramatic",
		values: {
			"hue-rotate": 0,
			saturate: 140,
			brightness: 90,
			contrast: 140,
			grayscale: 0,
			sepia: 0,
			blur: 0,
		},
	},
];

const filterStyle = computed(() => {
	return filters.value
		.map(filter => `${filter.name}(${filter.value}${filter.unit})`)
		.join(" ");
});

const cssCode = computed(() => {
	return `filter: ${filterStyle.value};`;
});

const handleImageUpload = event => {
	const file = event.target.files[0];
	if (file) {
		imageUrl.value = URL.createObjectURL(file);
	}
};

const applyPreset = preset => {
	filters.value.forEach(filter => {
		filter.value = preset.values[filter.name];
	});
};

const copyCssCode = () => {
	navigator.clipboard
		.writeText(cssCode.value)
		.then(() => alert("CSS code copied to clipboard!"))
		.catch(err => console.error("Failed to copy CSS code:", err));
};

const downloadImage = () => {
	const canvas = document.createElement("canvas");
	const ctx = canvas.getContext("2d");
	const img = new Image();

	img.onload = () => {
		canvas.width = img.width;
		canvas.height = img.height;
		ctx.filter = filterStyle.value;
		ctx.drawImage(img, 0, 0, img.width, img.height);

		canvas.toBlob(blob => {
			const url = URL.createObjectURL(blob);
			const a = document.createElement("a");
			a.href = url;
			a.download = "filtered_image.png";
			document.body.appendChild(a);
			a.click();
			document.body.removeChild(a);
			URL.revokeObjectURL(url);
		}, "image/png");
	};

	img.src = imageUrl.value;
};
</script>
