<script>
  import Calendar from '$lib/components/FullCalendar.svelte';
  import { onMount } from 'svelte';

  let events = [];
  onMount(() => {
    events = [
      { title: 'イベント1', start: '2025-06-01' },
      { title: 'イベント2', start: '2025-06-15', end: '2025-06-18' }
    ];
  });
</script>

<main class="p-4">
  <h1 class="text-2xl font-bold text-center mb-4">カレンダー</h1>
  <Calendar {events} />
</main>

<style>
  main {
    font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif;
    color: #333;
  }
</style>
```
