<script>
  import svelteLogo from './assets/svelte.svg'
  import viteLogo from '/vite.svg'
  import Counter from './lib/Counter.svelte'

import { load } from '@cashfreepayments/deviceintel-js-sdk';

let deviceData = null;
let loading = false;
let error = '';
let sdkTime = null;

async function collectDeviceData() {
  loading = true;
  error = '';
  deviceData = null;
  sdkTime = null;
  try {
    const start = performance.now();
    const agent = await load();
    const data = await agent.get({
      taskTimeout: 500,
      overallTimeout: 1000,
      options: { user_ip: false }
    });
    const end = performance.now();
    sdkTime = (end - start).toFixed(2);
    deviceData = data;
  } catch (e) {
    error = e?.message || 'Failed to collect device data.';
  }
  loading = false;
}
</script>

<main>
  <!-- Removed Vite/Svelte logos, heading, counter, and extra info as requested -->

  <button on:click={collectDeviceData} disabled={loading} style="margin-top:2em;">
    {loading ? 'Collecting...' : 'Collect Device Intelligence Data'}
  </button>
  {#if error}
    <p style="color:red">{error}</p>
  {/if}
  {#if deviceData}
    <table style="margin-top:1em; width:100%; max-width:800px; border-collapse:collapse; background:#f6f8fa; border-radius:8px; overflow:hidden;">
      <thead>
        <tr style="background:#eaeaea;">
          <th style="text-align:left; padding:8px; border-bottom:1px solid #ddd;">Key</th>
          <th style="text-align:left; padding:8px; border-bottom:1px solid #ddd;">Value</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td style="padding:8px; border-bottom:1px solid #eee; font-weight:bold;">SDK Time (ms)</td>
          <td style="padding:8px; border-bottom:1px solid #eee; word-break:break-word;">{sdkTime}</td>
        </tr>
        {#each Object.entries(deviceData) as [key, value]}
          <tr>
            <td style="padding:8px; border-bottom:1px solid #eee; font-weight:bold;">{key}</td>
            <td style="padding:8px; border-bottom:1px solid #eee; word-break:break-word;">{typeof value === 'object' ? JSON.stringify(value) : String(value)}</td>
          </tr>
        {/each}
      </tbody>
    </table>
  {/if}
</main>

<style>
  .logo {
    height: 6em;
    padding: 1.5em;
    will-change: filter;
    transition: filter 300ms;
  }
  .logo:hover {
    filter: drop-shadow(0 0 2em #646cffaa);
  }
  .logo.svelte:hover {
    filter: drop-shadow(0 0 2em #ff3e00aa);
  }
  .read-the-docs {
    color: #888;
  }
</style>
