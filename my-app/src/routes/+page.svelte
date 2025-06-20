<script lang="ts">
	import { onMount } from 'svelte';

	let city = "";
	let inputCity = "";
	let weatherData: any = null;

	const apiKey = "d6fb02024500cd1d66d8d6aa60023ed5";

	async function fetchWeatherByCity(name: string) {
		try {
			const res = await fetch(
				`https://api.openweathermap.org/data/2.5/weather?q=${name}&appid=${apiKey}&units=metric`
			);
			const data = await res.json();
			if (data.cod === 200) {
				city = data.name;
				weatherData = data;
			} else {
				console.error("City not found:", data.message);
				weatherData = null;
			}
		} catch (err) {
			console.error("Fetch error:", err);
			weatherData = null;
		}
	}

	async function fetchWeatherByCoords(lat: number, lon: number) {
		try {
			const res = await fetch(
				`https://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${lon}&appid=${apiKey}&units=metric`
			);
			const data = await res.json();
			if (data.cod === 200) {
				city = data.name;
				weatherData = data;
			}
		} catch (err) {
			console.error("Error with geolocation fetch:", err);
		}
	}

	onMount(() => {
		navigator.geolocation.getCurrentPosition(
			(pos) => {
				const { latitude, longitude } = pos.coords;
				fetchWeatherByCoords(latitude, longitude);
			},
			(err) => {
				console.error("Geolocation failed:", err);
				fetchWeatherByCity("Nairobi"); // Fallback
			}
		);
	});

	function handleSearch() {
		if (inputCity.trim().length > 1) {
			fetchWeatherByCity(inputCity.trim());
		}
	}
</script>

<main class="min-h-screen flex flex-col items-center justify-start p-6 bg-white gap-6">
	<h1 class="text-3xl font-bold text-gray-800">🌤️ Weather in {city || "..."}</h1>

	<!-- Search Input -->
	<div class="flex gap-2 w-full max-w-md">
		<input
			bind:value={inputCity}
			placeholder="Enter city"
			class="w-full px-3 py-2 rounded-md border text-sm"
			on:keydown={(e) => e.key === 'Enter' && handleSearch()}
		/>
		<button
			on:click={handleSearch}
			class="px-4 py-2 bg-blue-600 hover:bg-blue-700 text-white rounded-md text-sm"
		>
			Search
		</button>
	</div>

	<!-- Weather Card -->
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
</main>
