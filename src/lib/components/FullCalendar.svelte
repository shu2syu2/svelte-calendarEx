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
  /* 全セルの罫線リセット */
  :global(.fc th),
  :global(.fc td) {
    border: none !important;
    padding: 0 !important;
  }

  /* カレンダー外枠（角丸付き） */
  :global(.fc .fc-scrollgrid) {
    border: 1px solid #099999 !important;
    overflow: hidden;
  }

  /* ヘッダー行（曜日列）のグリッド線 */
  :global(.fc .fc-col-header-cell) {
    border-right: 1px solid white !important;
    background-color: #099999 !important;
    color: white !important;
    font-weight: normal !important;
    padding: 8px 0 !important;
    }

  /* 日付セルを囲うグリッド線 */
  :global(.fc .fc-scrollgrid-section-body td) {
    border: 1px solid #099999 !important;
  }

  /* 日付セルのグリッド線 */
  :global(.fc .fc-daygrid-day-frame) {
    border-bottom: 1px solid #099999 !important;
    border-right: 1px solid #099999 !important;
    /* border: 2px solid #099999 !important; */
    /* border:3px solid transparent; */
  }
  /* 最終行・最終列の調整 */
  /* 最終行の右側の線がでるので、それを消す すると他の縦線も細くなる*/
  :global(.fc .fc-daygrid-day-frame:last-child) {
    border-right: none !important;
  }
  /* 画面一番下の線の太さが変わる*/
  :global(.fc .fc-scrollgrid-section-body tr:last-child .fc-daygrid-day-frame) {
    border-bottom: 1px solid #099999 !important;
  }

  /* ヘッダーと日付行全体を囲う外枠（角丸付き） */
  /* コメントアウトすると上下左右橋が角 */
  /* border入れても無意味 */
  :global(.fc .fc-scrollgrid) {
    border: 1px solid #099999 !important;
    border-radius: 1rem !important;
    overflow: hidden;
  }

  /* コメントアウトすると横幅いっぱいだが、曜日は半分くらいにしか行かない */
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