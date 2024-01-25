<script lang="ts">
  import { onMount } from 'svelte';

	let email: string = '';
	let emailInput: HTMLInputElement;
	let emailSubmitted: boolean = false;
	$: isEmailValid = emailInput && email ? emailInput.validity.valid : true;

  let voted: Record<string, boolean> = {
    fruit: false,
    drink: false,
    animal: false
  }; 

  onMount(() => {
  	const storedEmail = localStorage.getItem('submittedEmail');
  	if (storedEmail) {
    	email = storedEmail;
  	}

    emailSubmitted = localStorage.getItem('emailSubmitted') === 'true';
    voted = {
      fruit: localStorage.getItem('fruit') === 'true',
      drink: localStorage.getItem('drink') === 'true',
      animal: localStorage.getItem('animal') === 'true'
    };
  });

  function submitEmail(): void {
    if (email) {
      emailSubmitted = true;
      localStorage.setItem('emailSubmitted', 'true');
      localStorage.setItem('submittedEmail', email);
    }
  }

  const categories: Record<string, string[]> = {
	  fruit: ['🍎', '🍌', '🍇', '🍓', '🍍', '🥭', '🍑', '🍒'],
	  drink: ['🍹', '🍺', '🍵', '☕', '🥤', '🍸', '🍷', '🥛'],
	  animal: ['🐶', '🐱', '🐭', '🐰', '🐻', '🦁', '🐨', '🐼']
  };

  function vote(category: string): void {
    voted[category] = true;
    localStorage.setItem(category, 'true');
  }

  function clearLocalStorage(): void {
	  localStorage.removeItem('emailSubmitted');
	  localStorage.removeItem('submittedEmail');
	  localStorage.removeItem('fruit');
	  localStorage.removeItem('drink');
	  localStorage.removeItem('animal');
	  emailSubmitted = false;
	  email = '';
	  voted = { fruit: false, drink: false, animal: false };
	}

	let showModal = false;

  $: {
    showModal = Object.values(voted).every(v => v);
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
    border:  1px solid #555;
    border-radius: 15px;
    font-size: 16px;
    background-color: white;
    cursor: pointer;
  }

  .email-input {
    width: 200px; 
  }

  .email-input:disabled, .submit-button:disabled {
    background-color: #e3e1e1; 
    cursor: default; 
  }

  .category {
    margin: 20px 0;
  }

  .category button {
    padding: 10px 20px;
    margin: 5px;
    border: 1px solid #ccc;
    border-radius: 15px;
    font-size: 36px;
    background-color: white;
    cursor: pointer;
    transition: all 0.2s ease;
  }

  .category button:active:not(:disabled) {
  	transform: scale(1.8);
	}

  .category button:disabled {
    color: #555;
    border-color: #555;
    background-color: #e3e1e1;
    cursor: default;
  }

  .category button:not(:disabled) {
    color: black;
    border-color: black;
  }

  .blur {
    filter: blur(2px); 
  }

	.modal {
	  position: fixed;
	  z-index: 1;
	  left: 0;
	  top: 0;
	  width: 100%;
	  height: 100%;
	  overflow: auto;
	  background-color: rgb(0,0,0); /* Fallback color */
	  background-color: rgba(0,0,0,0.4); /* Black w/ opacity */
	}

	.modal-content {
	  background-color: #fefefe;
	  margin: 15% auto;
	  padding: 20px;
	  border: 2px solid black;
	  border-radius: 15px;
	  width: 80%;
	  border-radius: 15px;

	}

	.close {
	  color: #aaa;
	  float: right;
	  font-size: 28px;
	  font-weight: bold;
	}

	.close:hover,
	.close:focus {
	  color: black;
	  text-decoration: none;
	  cursor: pointer;
	}

	.invalid {
    border-color: red; /* Red border for invalid input */
    background-color: #ffcccc; /* Light red background for invalid input */
  }
</style>


<div class="container">
	<input 
	  class="email-input {email && !emailInput.validity.valid ? 'invalid' : ''}"
	  type="email"
	  bind:value={email}
	  bind:this={emailInput}
	  placeholder="Enter your email"
	  disabled={emailSubmitted} />
  <button 
    class="submit-button"
    on:click={submitEmail}
    disabled={emailSubmitted || !isEmailValid}>
    Submit 
  </button>

		{#each Object.entries(categories) as [category, options]}
		<div class="category {emailSubmitted ? '' : 'blur'}" >
			<h3>{category[0].toUpperCase() + category.slice(1)}</h3>
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

{#if showModal}
  <div class="modal">
    <div class="modal-content">
      <span class="close" on:click={() => showModal = false}>&times;</span>
      <p>All categories have been voted on!</p>
      <a href="javascript:void(0)" on:click={clearLocalStorage}>Clear all data</a>
    </div>
  </div>
{/if}


<a href="javascript:void(0)" on:click={clearLocalStorage}>Clear all data</a>

</div>

