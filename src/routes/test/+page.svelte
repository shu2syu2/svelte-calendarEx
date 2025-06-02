<script>
  import FullCalendar from 'svelte-fullcalendar';
  import dayGridPlugin from '@fullcalendar/daygrid';
  import interactionPlugin from '@fullcalendar/interaction';
  import listPlugin from '@fullcalendar/list';
  // FullCalendar のスタイルを読み込む
  import "@fullcalendar/common/main.css";
  import '$lib/styles/global.css';

  import { createEventDispatcher } from 'svelte';

  export let events = [];
  export let initialView = 'dayGridMonth';
  export let editable = true;
  const dispatch = createEventDispatcher();

  // 「既に描画で見た他月」を覚えておくセット
  let seenOtherMonths = new Set();

  // view 切り替え時にリセット
  function handleDatesSet() {
    seenOtherMonths.clear();
  }

  // オプションをリアクティブに生成
  $: options = {
    plugins: [dayGridPlugin, interactionPlugin, listPlugin],
    initialView,
    headerToolbar: {
      left: 'prev,next today',
      center: 'title',
      right: 'dayGridMonth'
    },
    locale: 'ja',
    selectable: true,
    editable,
    events,
    height: 'auto',
    datesSet: handleDatesSet,
    dayCellContent: ({ date, isOther }) => {
      const m = date.getMonth() + 1;
      const d = date.getDate();
      if (!isOther && d === 1) {
        return { html: `${m}/${d}` };
      }
      if (isOther) {
        if (!seenOtherMonths.has(m)) {
          seenOtherMonths.add(m);
          return { html: `${m}/${d}` };
        }
        return { html: `${d}` };
      }
      return { html: `${d}` };
    }
  };
</script>

<style>
  
</style>

<div class="calendar-container">
  <FullCalendar {options}
    on:dateClick={e => dispatch('dateClick', e.detail)}
    on:eventClick={e => dispatch('eventClick', e.detail)}
  />
</div>