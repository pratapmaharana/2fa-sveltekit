<script>
	import { onMount } from 'svelte';
	import { fly } from 'svelte/transition';

	let code = ['', '', '', '', '', '']; // The entered code
	let isWrongCode = false; // Flag for incorrect code
	let isCodeMatched = false; // Flag for correct code match
	let focusedIndex = 0; // Tracks the focused input box
	let showUnderscore = [true, false, false, false, false, false]; // Underscore visibility control

	const correctCode = '628593'; // Correct code for validation

	// Reactive statement for code match validation
	$: codeDigitsLeft = 6 - code.filter(Boolean).length;

	// Check for matching code when all digits are entered
	$: {
		if (codeDigitsLeft === 0) {
			const enteredCode = code.join('');
			isCodeMatched = enteredCode === correctCode;
			isWrongCode = !isCodeMatched;

			if (isWrongCode) {
				vibrateAndReset();
			}
		}
	}

	// Handle vibration effect and reset the input fields
	function vibrateAndReset() {
		setTimeout(() => {
			code = ['', '', '', '', '', ''];
			showUnderscore = [true, false, false, false, false, false];
			isWrongCode = false;
			focusedIndex = 0;
			focusInputBox(focusedIndex);
		}, 500); // Adjust to match CSS animation duration
	}

	// Focus the next input box based on index
	function focusInputBox(index) {
		focusedIndex = index;
		document.querySelector(`input[data-index="${index}"]`)?.focus();
	}

	// Handle keydown events (e.g., for navigation)
	function handleKeydown(event) {
		const index = +event.target.dataset.index;
		if (event.key === 'ArrowRight' && index < code.length - 1) {
			focusInputBox(index + 1);
		} else if (event.key === 'ArrowLeft' && index > 0) {
			focusInputBox(index - 1);
		} else if (event.key === 'Backspace' && !code[index] && index > 0) {
			code[index - 1] = '';
			showUnderscore.fill(false);
			showUnderscore[index - 1] = true;
			focusInputBox(index - 1);
		}
	}

	// Handle input for code digits
	function handleCodeInput(event) {
		const { value, dataset } = event.target;
		const index = +dataset.index;
		const sanitizedValue = value.trim().replace(/\D/g, '');

		if (sanitizedValue) {
			code[index] = sanitizedValue;
			showUnderscore.fill(false);
			if (index < code.length - 1) showUnderscore[index + 1] = true;
			focusInputBox(index + 1);
		} else {
			event.target.value = '';
			code[index] = '';
			showUnderscore[index] = true;
		}
	}

	// Handle paste event for input
	function handlePaste(event) {
		const pasteData = event.clipboardData
			.getData('text')
			.split('')
			.filter((char) => /^\d$/.test(char));

		pasteData.forEach((digit, i) => {
			if (i < code.length) {
				code[i] = digit;
				showUnderscore.fill(false);
				if (i < code.length - 1) showUnderscore[i + 1] = true;
			}
		});

		document.querySelector('input[data-index="0"]').focus();
	}

	// Handle focus on input
	function handleFocus(index) {
		focusedIndex = index;
		showUnderscore.fill(false);
		showUnderscore[index] = true;
	}

	// Handle blur (when input loses focus)
	function handleBlur() {
		focusedIndex = -1;
	}

	// Initial setup to focus the first input box
	onMount(() => {
		focusInputBox(focusedIndex);
	});
</script>

<div
	class="p-8 flex flex-col items-center justify-center bg-white max-w-lg text-center rounded-2xl mx-auto"
>
	<!-- Lock Icon Section -->
	<div
		class="h-24 w-24 flex items-center justify-center rounded-full mb-8
		{isCodeMatched ? 'bg-green-100' : isWrongCode ? 'bg-red-100' : 'bg-gray-100'}"
	>
		{#if isCodeMatched}
			<img width="50" height="50" src="https://img.icons8.com/ios/22c55e/lock-2.png" alt="lock-2" />
		{:else if isWrongCode}
			<img width="50" height="50" src="https://img.icons8.com/ios/cc3333/lock-2.png" alt="lock-2" />
		{:else}
			<img
				width="50"
				height="50"
				src={`https://img.icons8.com/ios/0ea5e9/lock-2.png`}
				alt="lock-2"
			/>
		{/if}
	</div>

	<!-- Title and Instructions -->
	<h2 class="mb-4 text-3xl md:text-5xl font-semibold">Easy peasy</h2>
	<p class="mb-8 text-gray-400">Enter 6-digit code from your two-factor authentication app.</p>

	<div class:shake={isWrongCode}>
		<!-- Input Boxes with Flying Animations -->
		<div class="mb-8 flex items-center justify-between gap-2 w-full" on:paste={handlePaste}>
			{#each code as digit, index}
				<div
					class="w-12 h-16 md:w-14 md:h-20 text-center text-2xl md:text-3xl flex items-center justify-center border-2 rounded-lg relative overflow-hidden
					{isWrongCode
						? 'border-red-500'
						: focusedIndex === index || code[index]
							? 'border-sky-500'
							: 'border-gray-300'}"
				>
					<!-- Flying Underscore -->
					{#if focusedIndex === index && !code[index]}
						<div
							class="underscore bottom-2 w-6 h-0.5 bg-sky-500"
							in:fly={{ y: 50, duration: 500 }}
						></div>
					{/if}

					<!-- Flying Text -->
					{#if code[index]}
						<div
							class="flying-text text-2xl md:text-3xl {isWrongCode
								? 'text-red-500'
								: 'text-sky-500'}"
							in:fly={{ y: -50, duration: 500 }}
						>
							{code[index]}
						</div>
					{/if}

					<!-- Invisible Input -->
					<input
						type="text"
						bind:value={code[index]}
						class="absolute top-0 left-0 w-full h-full opacity-0"
						maxlength="1"
						data-index={index}
						on:keydown={handleKeydown}
						on:input={handleCodeInput}
						on:focus={() => handleFocus(index)}
						on:blur={handleBlur}
					/>
				</div>

				{#if index === 2}
					<div class="w-2 md:w-3"></div>
				{/if}
			{/each}
		</div>

		<!-- Submit Button -->
		<button
			class="w-full py-3 md:py-4 px-8 md:px-12 text-lg md:text-xl font-semibold rounded transition duration-300 ease-in-out focus:outline-none {isCodeMatched
				? 'fill-animation text-white'
				: isWrongCode
					? 'text-white bg-red-500 hover:bg-red-700'
					: 'text-gray-400 bg-gray-200'}"
		>
			<span class="relative z-10">
				{#if isCodeMatched}
					Let's Go
				{:else if isWrongCode}
					Wrong Code
				{:else if codeDigitsLeft > 0}
					{codeDigitsLeft}
					{#if codeDigitsLeft > 1}digits left{:else}digit left{/if}
				{/if}
			</span>
		</button>
	</div>
</div>

<style>
	.underscore,
	.flying-text {
		@apply absolute transition-all duration-300;
	}

	/* Vibration effect */
	.shake {
		animation: shake 0.3s;
	}

	@keyframes shake {
		0%,
		100% {
			transform: translateX(0);
		}
		25% {
			transform: translateX(-5px);
		}
		50% {
			transform: translateX(5px);
		}
		75% {
			transform: translateX(-5px);
		}
	}

	/* Right-to-left fill animation for the button */
	.fill-animation {
		position: relative;
		overflow: hidden;
	}

	.fill-animation::after {
		content: '';
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		background-color: #0284c7; /* Sky color */
		transform: translateX(100%);
		animation: fill-animation 0.3s forwards ease-in-out;
	}

	/* Keyframes for the right-to-left fill */
	@keyframes fill-animation {
		0% {
			transform: translateX(100%);
		}
		100% {
			transform: translateX(0);
		}
	}
</style>
