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
  /* 全セルの罫線非表示 */
  :global(.fc th),
  :global(.fc td) {
    border: none !important;
  }

	:global(
		.fc .fc-scrollgrid,
		.fc .fc-scrollgrid-section,
		.fc .fc-scrollgrid-section-body,
		.fc .fc-scrollgrid-section-body th,
		.fc .fc-scrollgrid-section-body td,
		.fc .fc-scrollgrid-section-body tr,
		.fc .fc-daygrid-day-frame,
		.fc .fc-col-header-cell
	) {
		border: none !important;
	}

  /* 曜日列（ヘッダー）にグリッド線を表示 */
  :global(.fc .fc-col-header-cell) {
    border-bottom: 2px solid #099999 !important;
    border-right: 2px solid #099999 !important;
    background-color: #099999 !important;
    color: white !important;
    font-weight: normal !important;
  }

  /* ヘッダーと日付行全体を囲う線（角丸付き） */
  :global(.fc .fc-scrollgrid) {
    border: 2px solid #099999 !important;
    border-collapse: collapse !important;
    border-radius: 0.5rem !important;
    overflow: hidden !important; /* 角丸を適用 */
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