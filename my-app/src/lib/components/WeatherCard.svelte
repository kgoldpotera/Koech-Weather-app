<script lang="ts">
	export let city: string;
	export let weatherData: any;
</script>

<div class="w-full max-w-md p-6 bg-gray-100 rounded-xl shadow space-y-3">
	{#if weatherData}
		<div class="space-y-1">
			<h2 class="text-xl font-semibold">Weather in {city}</h2>
			<p class="text-sm text-gray-700">Condition: {weatherData.weather[0].description}</p>
			<p class="text-sm text-gray-700">Temperature: {weatherData.main.temp}°C</p>
			<p class="text-sm text-gray-700">Humidity: {weatherData.main.humidity}%</p>
			<p class="text-sm text-gray-700">Wind: {weatherData.wind.speed} m/s</p>
		</div>
	{:else}
		<p class="text-sm text-gray-500">Loading or not found.</p>
	{/if}
</div>
