<script>
	import { onMount } from 'svelte';
	import Chart from 'chart.js/auto';

	let ticker = 'AAPL'; // Default to AAPL (supported symbol)
	let error = '';
	/**
	 * @type {Chart<"line", any, unknown> | null}
	 */
	let chart = null;
	/**
	 * @type {import("chart.js").ChartItem}
	 */
	let canvas;

	/**
	 * Fetches stock data for a given ticker symbol from Finnhub
	 * @param {string} ticker - The stock ticker symbol
	 * @returns {Promise<StockDataPoint[]>} An array of stock data points
	 */
	const getStockData = async (ticker) => {
		try {
			const now = Math.floor(Date.now() / 1000);
			const startOfDay = Math.floor(new Date().setHours(0, 0, 0, 0) / 1000);
			const apiKey = import.meta.env.VITE_FINNHUB_API_KEY;
			const url = `https://finnhub.io/api/v1/stock/candle?symbol=${ticker.toUpperCase()}&resolution=1&from=${startOfDay}&to=${now}&token=${apiKey}`;

			const response = await fetch(url);
			if (!response.ok) {
				if (response.status === 403) {
					throw new Error('Access denied: Invalid API key or insufficient permissions.');
				}
				throw new Error(`API request failed: ${response.statusText}`);
			}

			const data = await response.json();
			if (data.s === 'no_data') {
				throw new Error(`No data available for ${ticker}.`);
			}

			return data.t.map((/** @type {number} */ timestamp, /** @type {string | number} */ index) => ({
				date: new Date(timestamp * 1000).toLocaleTimeString(),
				close: data.c[index]
			}));
		} catch (/** @type {{ error: unknown; }} */ error) {
			console.error('Error fetching stock data:', error.message);
			error = error.message; // Set error for display
			return [];
		}
	};

	const updateChart = async () => {
		error = ''; // Reset error
		const data = await getStockData(ticker);
		const labels = data.map((item) => item.date);
		const prices = data.map((item) => item.close);

		if (chart) {
			chart.destroy();
		}

		if (data.length > 0 && canvas) {
			chart = new Chart(canvas, {
				type: 'line',
				data: {
					labels: labels,
					datasets: [
						{
							label: `${ticker.toUpperCase()} Closing Price`,
							data: prices,
							borderColor: '#3182ce',
							backgroundColor: 'rgba(49, 130, 206, 0.2)',
							fill: true,
							tension: 0.4
						}
					]
				},
				options: {
					responsive: true,
					scales: {
						x: {
							title: { display: true, text: 'Time', color: '#ffffff' },
							ticks: { color: '#ffffff' }
						},
						y: {
							title: { display: true, text: 'Price (USD)', color: '#ffffff' },
							ticks: { color: '#ffffff' }
						}
					},
					plugins: {
						legend: { labels: { color: '#ffffff' } }
					}
				}
			});
		}
	};

	onMount(() => {
		return () => {
			if (chart) chart.destroy();
		};
	});

	const handleSubmit = async () => {
		await updateChart();
	};
</script>

<svelte:head>
	<link rel="preconnect" href="https://fonts.googleapis.com" />
	<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
	<link
		href="https://fonts.googleapis.com/css2?family=PT+Serif:ital,wght@0,400;0,700;1,400&display=swap"
		rel="stylesheet"
	/>
	<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
</svelte:head>

<div class="container">
	<h1>Stock Ticker Lookup</h1>
	<div class="input-container">
		<input
			type="text"
			bind:value={ticker}
			placeholder="Enter stock ticker (e.g., AAPL)"
			class="ticker-input"
		/>
		<button on:click={handleSubmit} class="submit-button">Submit</button>
	</div>
	{#if error}
		<p class="error">{error}</p>
	{/if}
	<div class="chart-container">
		<canvas bind:this={canvas}></canvas>
	</div>
	<p class="disclosure">
		This is an experimental tool and does not provide financial advice or guaranteed accurate
		information.
	</p>
</div>

<style>
	.container {
		min-height: 100vh;
		background-color: #1a3c34;
		color: #ffffff;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		padding: 1rem;
		box-sizing: border-box;
		font-family: 'PT Serif', serif;
	}

	h1 {
		font-size: 2rem;
		font-weight: 700;
		margin-bottom: 1.5rem;
		font-family: 'PT Serif', serif;
	}

	.input-container {
		display: flex;
		gap: 0.5rem;
		width: 100%;
		max-width: 20rem;
	}

	.ticker-input {
		background-color: #2e4d47;
		border: 1px solid #4a5568;
		color: #ffffff;
		padding: 0.75rem;
		border-radius: 0.5rem;
		width: 100%;
		outline: none;
		font-family: 'PT Serif', serif;
		font-size: 1rem;
	}

	.submit-button {
		background-color: #3182ce;
		color: #ffffff;
		border: none;
		padding: 0.75rem 1.5rem;
		border-radius: 0.5rem;
		font-family: 'PT Serif', serif;
		font-size: 1rem;
		cursor: pointer;
	}

	.submit-button:hover {
		background-color: #2b6cb0;
	}

	.ticker-input::placeholder {
		color: #e2e8f0;
		opacity: 1;
	}

	.ticker-input:focus {
		border-color: #3182ce;
		box-shadow: 0 0 0 2px rgba(49, 130, 206, 0.5);
	}

	.chart-container {
		width: 100%;
		max-width: 40rem;
		margin: 1.5rem 0;
	}

	.disclosure {
		font-family: 'PT Serif', serif;
		font-size: 0.875rem;
		color: #a0aec0;
		text-align: center;
		margin-top: 1rem;
		max-width: 20rem;
	}

	.error {
		color: #ff4d4d;
		font-family: 'PT Serif', serif;
		font-size: 0.875rem;
		text-align: center;
		margin-top: 0.5rem;
	}

	:global(html, body) {
		margin: 0;
		padding: 0;
		height: 100%;
		width: 100%;
		background-color: #1a3c34;
		font-family: 'PT Serif', serif;
	}

	:global(canvas) {
		background-color: #2e4d47;
		border-radius: 0.5rem;
		padding: 1rem;
	}
</style>
