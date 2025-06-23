<script lang="ts">
	export let city: string;
	export let weatherData: any;
	export let forecastData: any[] = [];
</script>

<div class="card-container">
	{#if weatherData}
		<div class="current">
			<h2 class="title">Weather in {city}</h2>
			<p class="info">Condition: {weatherData.weather[0].description}</p>
			<p class="info">Temperature: {weatherData.main.temp}°C</p>
			<p class="info">Humidity: {weatherData.main.humidity}%</p>
			<p class="info">Wind: {weatherData.wind.speed} m/s</p>
		</div>

		{#if forecastData.length > 0}
			<div class="forecast">
				<h3 class="forecast-title">5-Day Forecast</h3>
				<div class="forecast-list">
					{#each forecastData as item (item.dt)}
						<div class="forecast-item">
							<div class="forecast-date">
								{new Date(item.dt_txt).toLocaleDateString(undefined, {
									weekday: 'short',
									month: 'short',
									day: 'numeric'
								})}
							</div>
							<div class="forecast-temp">
								{Math.round(item.main.temp)}°C
							</div>
							<div class="forecast-cond">
								{item.weather[0].main}
							</div>
						</div>
					{/each}
				</div>
			</div>
		{/if}
	{:else}
		<p class="loading">Loading or not found.</p>
	{/if}
</div>

<style>
	.card-container {
		width: 100%;
		max-width: 28rem;
		padding: 1.5rem;
		background-color: rgba(255, 255, 255, 0.8);
		backdrop-filter: blur(6px);
		border-radius: 1rem;
		box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
		display: flex;
		flex-direction: column;
		gap: 1rem;
	}

	.title {
		font-size: 1.25rem;
		font-weight: 600;
		color: #111827;
	}

	.info {
		font-size: 0.875rem;
		color: #374151;
	}

	.loading {
		color: #6b7280;
		font-size: 0.875rem;
	}

	.forecast {
		border-top: 1px solid #d1d5db;
		padding-top: 1rem;
	}

	.forecast-title {
		font-size: 1rem;
		font-weight: 500;
		color: #1f2937;
		margin-bottom: 0.5rem;
	}

	.forecast-list {
		display: flex;
		justify-content: space-between;
		gap: 0.5rem;
		flex-wrap: wrap;
	}

	.forecast-item {
		flex: 1 1 4.2rem;
		background: #f9fafb;
		border-radius: 0.5rem;
		padding: 0.5rem;
		text-align: center;
		font-size: 0.75rem;
		color: #374151;
		box-shadow: 0 1px 3px rgba(0, 0, 0, 0.08);
	}

	.forecast-date {
		font-weight: 600;
	}

	.forecast-temp {
		margin-top: 0.25rem;
		font-weight: 500;
	}

	.forecast-cond {
		margin-top: 0.25rem;
		color: #6b7280;
		font-size: 0.7rem;
	}
</style>
