<script lang="ts">
	import { onDestroy, onMount } from "svelte";


	let card_data: string;
	let form: HTMLFormElement;
  let card_input_element: HTMLInputElement;

  onMount(() => {
    card_input_element.focus()
  }) 

  const timer = setInterval(() => {card_input_element.focus()}, 500)

  onDestroy(() => {
    clearInterval(timer)
  })

	const url_endpoint =
		'https://script.google.com/macros/s/AKfycbw3UTIj7DNo5hkMykXHfrH3sEUKtKShUgT9zqyOm_HCfn84orULQuxB83XXth2_l-lCKw/exec';

	const card_data_regex = /.*/;

	let card_error: undefined | boolean = undefined;

	function validateCardData(cardData: string): undefined | string {
		if (card_data === 'fail') return undefined;
		return cardData;
	}

	function formSubmit() {
		console.log('submit');
		const valid_code_data = validateCardData(card_data);

		if (valid_code_data === undefined) {
			handle_error();
			return;
		}

		handle_success(valid_code_data);
		form.reset();
	}

	function handle_error() {
		card_error = true;

		setTimeout(() => {
			card_error = undefined;
		}, 2000);
	}

	async function handle_success(code_data: string) {
		card_error = false;

		const this_url = new URL(url_endpoint);

		const params = {
      endpoint: "insertOneStatus",
			userId: code_data,
			Date: new Date().toLocaleString("en-US").replace(",", "") // Saturday, September 17, 2016

		};

		this_url.search = new URLSearchParams(params).toString();

    console.log(this_url)
		let fetch_res = await fetch(this_url, {
			method: 'POST'
		});

    console.log(fetch_res.status)
    console.log(await fetch_res.text())

		setTimeout(() => {
			card_error = undefined;
		}, 2000);
	}
</script>

<form bind:this={form} on:submit|preventDefault={formSubmit}>
	<label for="cardData">Enter UID Here</label>
	<input bind:this={card_input_element} name="cardData" bind:value={card_data} class="form-input" />
	<input type="submit" style="display: none" />
</form>

{#if card_error === true}
	ERROR
{:else if card_error === false}
	SUCCESS
{:else}
	<!-- else content here -->
{/if}
