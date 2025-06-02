<script>
  import FullCalendar from 'svelte-fullcalendar';
  import dayGridPlugin from '@fullcalendar/daygrid';
  import interactionPlugin from '@fullcalendar/interaction';
  import listPlugin from '@fullcalendar/list';
  // FullCalendar のスタイルを読み込む
  import "@fullcalendar/common/main.css";

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
  /* 全てのテーブル罫線とスペーシングをリセット */
  :global(.fc table) {
    border-collapse: collapse !important;
    border-spacing: 0 !important;
  }
  /* 全セルの罫線非表示 */
  :global(.fc th),
  :global(.fc td) {
    border: none !important;
  }

  /* イベント枠も非表示 */
  :global(.fc .fc-daygrid-event),
  :global(.fc .fc-event-main-frame) {
    border: none !important;
    box-shadow: none !important;
  }

  .calendar-container {
    max-width: 900px;
    margin: 2rem auto;
    background: white;
    border-radius: 0.5rem;
    box-shadow: 0 2px 8px rgba(0,0,0,0.1);
    padding: 1rem;
  }
</style>

<div class="calendar-container">
  <FullCalendar {options}
    on:dateClick={e => dispatch('dateClick', e.detail)}
    on:eventClick={e => dispatch('eventClick', e.detail)}
  />
</div>