<script>
  import { onMount } from 'svelte';

  let currencies = [];
  let baseCurrency = 'USD';
  let targetCurrency = 'EUR';
  let amount = 1;
  let convertedAmount = 0;
  

  // Fetch the list of available currencies on mount
  onMount(async () => {
      const response = await fetch(`https://open.er-api.com/v6/latest`);
      const data = await response.json();
      if (data.result === "success") {
          currencies = Object.keys(data.rates);
      }
  });

  // Convert currency based on input values
  async function convertCurrency() {
      const response = await fetch(
          `https://open.er-api.com/v6/latest/${baseCurrency}`
      );
      const data = await response.json();
      if (data.result === "success") {
          const rate = data.rates[targetCurrency];
          convertedAmount = amount * rate;
      } else {
          alert('Error fetching conversion data');
      }
  }
</script>

<style>
  

  h1 {
    color:orange;
  }

  .converter {
      max-width: 400px;
      margin: 50px auto;
      padding: 20px;
      background: #fff;
      border-radius: 8px;
      border-style: solid;
      border-color: rgb(229, 255, 0);
      box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  }

  label {
      display: block;
      margin-bottom: 8px;
      font-weight: bold;
  }

  select, input, button {
      width: 100%;
      padding: 10px;
      margin-bottom: 20px;
      border: 1px solid #ccc;
      border-radius: 4px;
  }

  button {
      background: #007BFF;
      color: #fff;
      font-weight: bold;
      cursor: pointer;
      transition: background 0.3s;
  }

  button:hover {
      background: #0056b3;
  }

  .result {
      font-size: 1.5em;
      text-align: center;
      margin-top: 20px;
  }
</style>

<div class="converter">
  <h1>Currency Converter</h1>

  <label for="baseCurrency">From:</label>
  <select id="baseCurrency" bind:value={baseCurrency}>
      {#each currencies as currency}
          <option value={currency}>{currency}</option>
      {/each}
  </select>

  <label for="targetCurrency">To:</label>
  <select id="targetCurrency" bind:value={targetCurrency}>
      {#each currencies as currency}
          <option value={currency}>{currency}</option>
      {/each}
  </select>

  <label for="amount">Amount:</label>
  <input id="amount" type="number" bind:value={amount} min="0" step="0.01" />

  <button on:click={convertCurrency}>Convert</button>

  {#if convertedAmount}
      <div class="result">Converted Amount: {convertedAmount.toFixed(2)} {targetCurrency}</div>
  {/if}
</div>
