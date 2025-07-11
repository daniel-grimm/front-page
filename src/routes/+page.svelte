<svelte:head>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=PT+Serif:ital,wght@0,400;0,700;1,400&display=swap" rel="stylesheet">
</svelte:head>

<script>
  let ticker = '';
</script>

<div class="container">
  <h1>Stock Ticker Lookup</h1>
  <input
    type="text"
    bind:value={ticker}
    placeholder="Enter stock ticker (e.g., AAPL)"
    class="ticker-input"
  />
</div>

<style>
  .container {
    min-height: 100vh;
    background-color: #1a202c;
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
    background-color: #2d3748;
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

  .ticker-input:focus {
    border-color: #3182ce;
    box-shadow: 0 0 0 2px rgba(49, 130, 206, 0.5);
  }

  :global(html, body) {
    margin: 0;
    padding: 0;
    height: 100%;
    width: 100%;
    background-color: #1a202c;
    font-family: 'PT Serif', serif;
  }
</style>