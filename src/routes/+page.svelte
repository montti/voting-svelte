<script lang="ts">
	let email: string = '';
	let emailSubmitted: boolean = false;

	function submitEmail(): void {
		if (email) {
			emailSubmitted = true;
		}
	}

	const categories: Record<string, string[]> = {
		fruit: ['🍎', '🍌', '🍇', '🍓', '🍍'],
		drink: ['🍹', '🍺', '🍵', '☕', '🍼'],
		animal: ['🐶', '🐱', '🐭', '🐹', '🐰']
	};

	let voted: Record<string, boolean> = {
		fruit: false,
		drink: false,
		animal: false
	};

	function vote(category: string): void {
		voted[category] = true;
	}
</script>

<style>
  .container {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    min-height: 100vh;
  }

  .email-input, .submit-button {
    padding: 10px 20px;
    margin: 5px;
    border: 1px solid #ccc;
    border-radius: 15px;
    font-size: 16px;
    background-color: white;
    cursor: pointer;
  }

  .email-input {
    width: 200px; /* Adjust as needed */
  }

  .category {
    margin: 20px 0;
  }

  .category button {
    padding: 10px 20px;
    margin: 5px;
    border: 1px solid #ccc;
    border-radius: 15px;
    font-size: 48px;
    background-color: white;
    cursor: pointer;
    transition: all 0.2s ease;
  }

  .category button:active:not(:disabled) {
  	transform: scale(1.8);
	}

  .category button:disabled {
    color: grey;
    border-color: grey;
    background-color: #f0f0f0;
    cursor: default;
  }

  .category button:not(:disabled) {
    color: black;
    border-color: black;
  }
</style>


<div class="container">
	<input 
		class="email-input"
		type="email" 
		bind:value={email}
		placeholder="Enter your email" 
	/>
	<button on:click={submitEmail}>Submit</button>

		{#each Object.entries(categories) as [category, options]}
		<div class="category" >
			<h3>{category}</h3>
			{#each options as option}
				<button 
					disabled={!emailSubmitted || voted[category]}
					on:click={() => vote(category)}
				>
					{option}
				</button>
			{/each}
		</div>
	{/each}

</div>

