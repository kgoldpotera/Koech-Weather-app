<script lang="ts">
	import AppSidebar from "$lib/components/app-sidebar.svelte";
	import * as Breadcrumb from "$lib/components/ui/breadcrumb/index.js";
	import { Separator } from "$lib/components/ui/separator/index.js";
	import * as Sidebar from "$lib/components/ui/sidebar/index.js";
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

	// Get user's location on load
	onMount(() => {
		navigator.geolocation.getCurrentPosition(
			(pos) => {
				const { latitude, longitude } = pos.coords;
				fetchWeatherByCoords(latitude, longitude);
			},
			(err) => {
				console.error("Geolocation failed:", err);
				// Fallback to default city
				fetchWeatherByCity("Nairobi");
			}
		);
	});

	function handleSearch() {
		if (inputCity.trim().length > 1) {
			fetchWeatherByCity(inputCity.trim());
		}
	}
</script>

<Sidebar.Provider>
	<AppSidebar />
	<Sidebar.Inset>
		<header class="group-has-data-[collapsible=icon]/sidebar-wrapper:h-12 flex h-16 items-center gap-2 transition-[width,height] ease-linear">
			<div class="flex items-center gap-2 px-4">
				<Sidebar.Trigger class="-ml-1" />
				<Separator orientation="vertical" class="mr-2 data-[orientation=vertical]:h-4" />
				<Breadcrumb.Root>
					<Breadcrumb.List>
						<Breadcrumb.Item class="hidden md:block">
							<Breadcrumb.Link href="#">Weather</Breadcrumb.Link>
						</Breadcrumb.Item>
						<Breadcrumb.Separator class="hidden md:block" />
						<Breadcrumb.Item>
							<Breadcrumb.Page>{city || "Loading..."}</Breadcrumb.Page>
						</Breadcrumb.Item>
					</Breadcrumb.List>
				</Breadcrumb.Root>
			</div>
		</header>

		<div class="flex flex-1 flex-col gap-4 p-4 pt-0">
			<div class="grid gap-4 md:grid-cols-1">
				<div class="p-4 bg-card text-card-foreground rounded-xl shadow border space-y-4">
					<!-- Search Input -->
					<div class="flex gap-2">
						<input
							bind:value={inputCity}
							placeholder="Enter city"
							class="w-full px-3 py-2 rounded-md border text-sm"
							on:keydown={(e) => e.key === 'Enter' && handleSearch()}
						/>
						<button
							on:click={handleSearch}
							class="px-4 py-2 bg-primary text-white rounded-md text-sm"
						>
							Search
						</button>
					</div>

					<!-- Weather Card -->
					{#if weatherData}
						<div class="space-y-1">
							<h2 class="text-xl font-semibold">Weather in {city}</h2>
							<p class="text-sm text-muted-foreground">
								Condition: {weatherData.weather[0].description}
							</p>
							<p class="text-sm text-muted-foreground">
								Temperature: {weatherData.main.temp}°C
							</p>
							<p class="text-sm text-muted-foreground">
								Humidity: {weatherData.main.humidity}%
							</p>
							<p class="text-sm text-muted-foreground">
								Wind: {weatherData.wind.speed} m/s
							</p>
						</div>
					{:else}
						<p class="text-sm text-muted-foreground">Loading or not found.</p>
					{/if}
				</div>
			</div>
		</div>
	</Sidebar.Inset>
</Sidebar.Provider>
