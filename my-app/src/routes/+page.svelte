<script lang="ts">
	import { onMount } from 'svelte';
	import WeatherCard from '$lib/components/WeatherCard.svelte';

	let city = "";
	let inputCity = "";
	let weatherData: any = null;
	let backgroundUrl: string | null = null;

	const weatherApiKey = "d6fb02024500cd1d66d8d6aa60023ed5";
	const unsplashAccessKey = "kly3TeRHFZdjy6YUv3j4Jtx99JjdorqCbdrvmC14nxE";

	// Use Unsplash search endpoint for city-specific results
	async function fetchBackgroundImage(query: string) {
		try {
			const res = await fetch(
				`https://api.unsplash.com/search/photos?query=${query}&client_id=${unsplashAccessKey}&orientation=landscape&per_page=1`
			);
			const data = await res.json();
			if (data?.results?.[0]?.urls?.regular) {
				backgroundUrl = data.results[0].urls.regular;
			} else {
				console.warn("No background image found for", query);
				backgroundUrl = null;
			}
		} catch (err) {
			console.error("Unsplash error:", err);
			backgroundUrl = null;
		}
	}

	async function fetchWeatherByCity(name: string) {
		try {
			const res = await fetch(
				`https://api.openweathermap.org/data/2.5/weather?q=${name}&appid=${weatherApiKey}&units=metric`
			);
			const data = await res.json();
			if (data.cod === 200) {
				city = data.name;
				weatherData = data;
				await fetchBackgroundImage(city);
			} else {
				console.error("City not found:", data.message);
				weatherData = null;
			}
		} catch (err) {
			console.error("Weather fetch error:", err);
			weatherData = null;
		}
	}

	async function fetchWeatherByCoords(lat: number, lon: number) {
		try {
			const res = await fetch(
				`https://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${lon}&appid=${weatherApiKey}&units=metric`
			);
			const data = await res.json();
			if (data.cod === 200) {
				city = data.name;
				weatherData = data;
				await fetchBackgroundImage(city);
			}
		} catch (err) {
			console.error("Geolocation weather fetch error:", err);
		}
	}

	onMount(() => {
		navigator.geolocation.getCurrentPosition(
			(pos) => {
				const { latitude, longitude } = pos.coords;
				fetchWeatherByCoords(latitude, longitude);
			},
			() => fetchWeatherByCity("Nairobi")
		);
	});

	function handleSearch() {
		if (inputCity.trim().length > 1) {
			fetchWeatherByCity(inputCity.trim());
		}
	}
</script>

<main class="main-container" style:background-image={`url('${backgroundUrl}')`}>
	<h1 class="app-title">🌤️ Weather in {city || "..."}</h1>

	<div class="search-container">
		<input
			bind:value={inputCity}
			placeholder="Enter city"
			class="search-input"
			on:keydown={(e) => e.key === 'Enter' && handleSearch()}
		/>
		<button on:click={handleSearch} class="search-button">Search</button>
	</div>

	<WeatherCard {city} {weatherData} />
</main>

<style>
	.main-container {
		min-height: 100vh;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: flex-start;
		padding: 1.5rem;
		gap: 1.5rem;
		background-size: cover;
		background-position: center;
		transition: background-image 0.5s ease-in-out;
	}

	.app-title {
		font-size: 1.875rem;
		font-weight: bold;
		color: white;
		text-shadow: 0 2px 6px rgba(0, 0, 0, 0.3);
	}

	.search-container {
		display: flex;
		gap: 0.5rem;
		width: 100%;
		max-width: 28rem;
		background: rgba(255, 255, 255, 0.9);
		backdrop-filter: blur(8px);
		padding: 0.5rem;
		border-radius: 0.375rem;
	}

	.search-input {
		flex-grow: 1;
		padding: 0.5rem 0.75rem;
		border-radius: 0.375rem;
		border: 1px solid #ccc;
		font-size: 0.875rem;
	}

	.search-button {
		padding: 0.5rem 1rem;
		background-color: #2563eb;
		color: white;
		border: none;
		border-radius: 0.375rem;
		cursor: pointer;
		font-size: 0.875rem;
	}

	.search-button:hover {
		background-color: #1d4ed8;
	}
</style>
