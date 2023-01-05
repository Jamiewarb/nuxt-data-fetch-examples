<script setup lang="ts">
import { useLazyAsyncData } from '#app';
import { Ref } from '@vue/reactivity';

const data: Ref = useState(() => null);
const noServerData: Ref = useState(() => null);

const { data: noServerLazyData } = useLazyAsyncData('async-example-lazy-no-server', async () => {
  console.log('Starting no server lazy useAsyncData');
  await delay(1500);
  const data = await $fetch('https://jsonplaceholder.typicode.com/posts/4');
  console.log('Finished no server lazy useAsyncData');
  return data;
}, { server: false });

const p2 = useAsyncData('async-examole-no-server', async () => {
  console.log('Starting no server useAsyncData');
  await delay(1500);
  const data = await $fetch('https://jsonplaceholder.typicode.com/posts/3');
  console.log('Finished no server useAsyncData');
  return data;
}, { server: false }).then((result) => noServerData.value = result.data);

const { data: lazyData } = useLazyAsyncData('async-example-lazy', async () => {
  console.log('Starting lazy useAsyncData');
  await delay(1500);
  const data = await $fetch('https://jsonplaceholder.typicode.com/posts/2');
  console.log('Finished lazy useAsyncData');
  return data;
});

const p1 = useAsyncData('async-example', async () => {
  console.log('Starting useAsyncData');
  await delay(1500);
  const data = await $fetch('https://jsonplaceholder.typicode.com/posts/1');
  console.log('Finished useAsyncData');
  return data;
}).then((result) => data.value = result.data);

await Promise.allSettled([p1, p2]);
</script>

<template>
  <h1>Fetch with Async Data</h1>
  <NuxtLink to="/">Back to home</NuxtLink>
  <h2>Page Explanation</h2>
  <p>We can use the composable `useAsyncData()` to help us gather asynchronous data, such as that from an API.</p>
  <p>There are a few ways to use this composable, which we'll explore.</p>

  <h1>Quick Comparison</h1>
  <p>This table shows you the results of the below in a quick reference format.</p>

  <p>Async: {{ data ? 'Complete' : '...'}}</p>
  <p>Lazy Async: {{ lazyData ? 'Complete' : '...'}}</p>
  <p>Async no server: {{ noServerData ? 'Complete' : '...'}}</p>
  <p>Lazy Async no server: {{ noServerLazyData ? 'Complete' : '...'}}</p>

  <h2>1. Async Data blocking page navigation</h2>
  <p>This data was loaded during SSR on hard refresh, or before navigation on client navigation</p>
  <p>You will see this data if you view the page source, and so would search engines</p>
  <code><pre>{{ data }}</pre></code>

  <h2>2. Async Data not blocking clientside page navigation (lazy)</h2>
  <p>This data was loaded after clientside hydration. A loading state should be implemented for lazy loading
    this data.</p>
  <p>This data will still block SSR, so will still block page load on a hard-refresh or first navigation, but will not block clientside navigation.</p>
  <p>An artificial delay of 1.5s was added to this API call to show the loading state.</p>
  <code><pre>{{ lazyData }}</pre></code>

  <h2>3. Async Data not rendered on server</h2>
  <p>This data was loaded after clientside hydration. A loading state should be implemented for lazy loading
    this data.</p>
  <p>This data will never be loaded during SSR on hard refresh, and will never block navigation, on hard-refresh or on clientside navigation.</p>
  <p>An artificial delay of 3s was added to this API call to show the loading state.</p>
  <code><pre>{{ noServerData }}</pre></code>

  <h2>4. Async Data not rendered on server and not blocking clientside page navigation (lazy)</h2>
  <p>This data was loaded after clientside hydration. A loading state should be implemented for lazy loading
    this data.</p>
  <p>This data will never be loaded during SSR on hard refresh, and will never block navigation, on hard-refresh or on clientside navigation.</p>
  <p>An artificial delay of 3s was added to this API call to show the loading state.</p>
  <code><pre>{{ noServerData }}</pre></code>
</template>

