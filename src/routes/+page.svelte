<script>
	import { onMount } from 'svelte';
	import Chart from 'chart.js/auto';

	let ticker = '';
	let chart = null;
	let canvas;

	// Simulated stock data (replace with real API call in production)
	const getStockData = async (ticker) => {
		// Mock data for demonstration
		const mockData = {
			'FXAIX': [
				{ date: '2025-01-01', close: 150.23 },
				{ date: '2025-02-01', close: 152.45 },
				{ date: '2025-03-01', close: 149.78 },
				{ date: '2025-04-01', close: 155.12 },
				{ date: '2025-05-01', close: 157.89 },
				{ date: '2025-06-01', close: 160.34 }
			]
		};
		return mockData[ticker.toUpperCase()] || [];
	};

	const updateChart = async () => {
		const data = await getStockData(ticker);
		const labels = data.map(item => item.date);
		const prices = data.map(item => item.close);

		if (chart) {
			chart.destroy();
		}

		if (data.length > 0 && canvas) {
			chart = new Chart(canvas, {
				type: 'line',
				data: {
					labels: labels,
					datasets: [{
						label: `${ticker.toUpperCase()} Closing Price`,
						data: prices,
						borderColor: '#3182ce',
						backgroundColor: 'rgba(49, 130, 206, 0.2)',
						fill: true,
						tension: 0.4
					}]
				},
				options: {
					responsive: true,
					scales: {
						x: {
							title: { display: true, text: 'Date', color: '#ffffff' },
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

	$: ticker, updateChart();

	onMount(() => {
		return () => {
			if (chart) chart.destroy();
		};
	});
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
	<input
		type="text"
		bind:value={ticker}
		placeholder="Enter stock ticker (e.g., FXAIX)"
		class="ticker-input"
	/>
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

	.ticker-input {
		background-color: #2e4d47;
		border: 1px solid #4a5568;
		color: #ffffff;
		padding: 0.75rem;
		border-radius: 0.5rem;
		width: 100%;
		max-width: 20rem;
		outline: none;
		font-family: 'PT Serif', serif;
		font-size: 1rem;
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