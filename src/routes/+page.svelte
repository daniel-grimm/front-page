<script lang="ts">
	let isDarkMode = false;

	// Placeholder function for toggling theme
	function toggleTheme() {
		isDarkMode = !isDarkMode;
		console.log(`Theme toggled to: ${isDarkMode ? 'Dark' : 'Light'}`);
	}

	// Placeholder function for opening settings
	function openSettings() {
		console.log('Settings menu opened');
		// Implement logic to show a modal or navigate to a settings page
	}

	let chatInput = '';

	// Placeholder function for sending message
	function sendMessage() {
		if (chatInput.trim()) {
			alert(`Sending message: ${chatInput}`);
			// Implement logic to send message to LLM below
			// ...
			chatInput = ''; // Clear input after sending
		}
	}

	// Allow sending message on Enter key press
	function handleKeydown(event: KeyboardEvent) {
		if (event.key === 'Enter' && !event.shiftKey) {
			event.preventDefault(); // Prevent newline in input
			sendMessage();
		}
	}
</script>

<div class="page-container" class:dark-mode={isDarkMode}>
	<header class="header">
		<div class="top-right-icons">
			<button class="icon-button" on:click={toggleTheme} aria-label="Toggle theme">
				{#if isDarkMode}
					☀️
				{:else}
					🌙
				{/if}
			</button>
			<button class="icon-button" on:click={openSettings} aria-label="Open settings"> ⚙️ </button>
		</div>
	</header>

	<main class="chat-area">
		<p>Chat messages will appear here...</p>
		{#each Array(20) as _, i}
			<p>Message {i + 1}</p>
		{/each}
	</main>

	<!-- Input area -->
	<footer class="input-area">
		<div class="input-container">
			<textarea
				bind:value={chatInput}
				on:keydown={handleKeydown}
				placeholder="Type your message..."
				rows={1}
			></textarea>
			<button on:click={sendMessage} disabled={!chatInput.trim()}>Send</button>
		</div>
	</footer>
</div>

<style>
	:root {
		--white: #fff;
		--black: #000;
		--dark-gray: #1c1c1c;
	}

	/* CSS variables for theme */
	.page-container {
		--bg-color: var(--white);
		--text-color: var(--black);
		--input-bg: #eee;
		--input-border: #ccc;
		--button-bg: #007bff;
		--button-text: #fff;
		--button-disabled-bg: #ccc;
		--button-disabled-text: #666;
		--header-bg: transparent; /* Header is transparent, icons float */
		--chat-bg: transparent; /* Chat area is transparent */

		display: flex;
		flex-direction: column;
		height: 100vh; /* Full viewport height */
		overflow: hidden; /* Prevent body scroll */
	}

	.page-container.dark-mode {
		--bg-color: var(--dark-gray);
		--text-color: #eee;
		--input-bg: #333;
		--input-border: #555;
		--button-bg: #0056b3;
		--button-text: #fff;
		--button-disabled-bg: #555;
		--button-disabled-text: #aaa;
	}

	.header {
		position: relative; /* Needed for absolute positioning of icons */
		width: 100%;
		padding: 10px;
		box-sizing: border-box;
		z-index: 10; /* Ensure icons are above chat area */
	}

	.top-right-icons {
		position: absolute;
		top: 10px;
		right: 10px;
		display: flex;
		gap: 10px; /* Space between icons */
	}

	.icon-button {
		background: none;
		border: none;
		font-size: 1.5em; /* Adjust size as needed */
		cursor: pointer;
		color: var(--text-color); /* Use text color for icons */
		padding: 5px;
		transition: color 0.2s ease;
	}

	.icon-button:hover {
		color: var(--button-bg); /* Simple hover effect */
	}

	.chat-area {
		flex-grow: 1; /* Takes up remaining space */
		overflow-y: auto; /* Enable scrolling for messages */
		padding: 20px;
		padding-top: 60px; /* Add padding to prevent content from being hidden by header */
		box-sizing: border-box;
		display: flex;
		flex-direction: column; /* Stack messages vertically */
		gap: 10px; /* Space between placeholder messages */
	}

	.input-area {
		width: 100%;
		padding: 10px 20px; /* Add horizontal padding */
		box-sizing: border-box;
		display: flex;
		justify-content: center; /* Center the input container */
		background-color: var(--bg-color); /* Match background */
		border-top: 1px solid var(--input-border); /* Separator line */
	}

	.input-container {
		display: flex;
		width: 100%;
		max-width: 800px; /* Max width for the input area */
		border: 1px solid var(--input-border);
		border-radius: 25px; /* Rounded corners */
		overflow: hidden; /* Keep children within border-radius */
		background-color: var(--input-bg);
	}

	.input-container textarea {
		flex-grow: 1; /* Textarea takes up most space */
		border: none;
		outline: none;
		padding: 10px 15px;
		font-size: 1em;
		resize: none; /* Disable textarea resize handle */
		background-color: transparent; /* Use container background */
		color: var(--text-color);
		min-height: 20px; /* Ensure minimum height */
		max-height: 150px; /* Prevent textarea from getting too tall */
		overflow-y: auto; /* Add scrollbar if content exceeds max-height */
	}

	.input-container button {
		padding: 10px 20px;
		background-color: var(--button-bg);
		color: var(--button-text);
		border: none;
		cursor: pointer;
		transition: background-color 0.2s ease;
	}

	.input-container button:hover:not(:disabled) {
		background-color: var(--button-bg); /* Keep same color for now, or slightly darker */
		opacity: 0.9;
	}

	.input-container button:disabled {
		background-color: var(--button-disabled-bg);
		color: var(--button-disabled-text);
		cursor: not-allowed;
	}
</style>
