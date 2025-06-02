<script>
  import FullCalendar from 'svelte-fullcalendar';
  import dayGridPlugin from '@fullcalendar/daygrid';
  import timeGridPlugin from '@fullcalendar/timegrid';
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
    plugins: [dayGridPlugin, interactionPlugin],
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
    // 高さを自動調整
    height: 'auto',
    // ここで見た他月をクリア
    datesSet: handleDatesSet,
    // 描画コンテンツを一度だけ決める
    dayCellContent: ({ date, isOther }) => {
      const m = date.getMonth() + 1;
      const d = date.getDate();
      // ・当月の１日 → 〈月／日〉
      if (!isOther && d === 1) {
        return { html: `${m}/${d}` };
      }
      // ・他月セル → 初回だけ〈月／日〉、以降は〈日〉
      if (isOther) {
        if (!seenOtherMonths.has(m)) {
          seenOtherMonths.add(m);
          return { html: `${m}/${d}` };
        }
        return { html: `${d}` };
      }
      // ・それ以外の当月セル →〈日〉だけ
      return { html: `${d}` };
    },
  };
</script>

<style>
  :global(.fc) {
    --fc-bg-event-opacity: 0.6;
    --fc-border-color: #4a90e2;
    --fc-highlight-color: #4a90e2;
    --fc-today-bg-color: #e8f4fd;
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