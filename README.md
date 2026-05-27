<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>M8 Center</title>
<script src="https://telegram.org/js/telegram-web-app.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800;900&family=Space+Grotesk:wght@500;700&display=swap" rel="stylesheet">
<style>
*,*::before,*::after{margin:0;padding:0;box-sizing:border-box;-webkit-tap-highlight-color:transparent}
:root{
  --bg:#05050a;
  --s1:rgba(255,255,255,.035);
  --s2:rgba(255,255,255,.06);
  --b1:rgba(255,255,255,.07);
  --b2:rgba(255,255,255,.15);
  --b3:rgba(255,255,255,.35);
  --a:#5046e5;
  --a2:rgba(80,70,229,.15);
  --a3:rgba(80,70,229,.3);
  --t1:#ffffff;
  --t2:#8b95a1;
  --t3:#3d4652;
  --green:#22c55e;
  --red:#ef4444;
}
html,body{height:100%;background:var(--bg);overflow:hidden}
body{font-family:'Inter',sans-serif;color:var(--t1)}

/* ── CANVAS ── */
#c{position:fixed;inset:0;z-index:0;opacity:.55}

/* ── GRAIN ── */
body::before{
  content:'';position:fixed;inset:0;z-index:1;pointer-events:none;
  background-image:url("data:image/svg+xml,%3Csvg viewBox='0 0 512 512' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='f'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='.85' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23f)' opacity='0.035'/%3E%3C/svg%3E");
  opacity:.7
}

/* ── INTRO ── */
#intro{
  position:fixed;inset:0;z-index:500;background:#000;
  display:flex;align-items:center;justify-content:center;flex-direction:column;gap:3rem
}
.i-logo{
  font-family:'Space Grotesk',sans-serif;font-size:1.2rem;font-weight:700;
  letter-spacing:.5em;text-transform:uppercase;color:#fff;opacity:0;
  transition:opacity .8s .2s
}
.i-bar{width:180px;height:1px;background:rgba(255,255,255,.1);position:relative;overflow:hidden}
.i-bar-fill{
  position:absolute;left:-100%;top:0;bottom:0;width:100%;
  background:linear-gradient(90deg,transparent,rgba(80,70,229,.8),#fff,rgba(80,70,229,.8),transparent);
  transition:left 2s cubic-bezier(.23,1,.32,1)
}
.i-num{
  position:absolute;font-family:'Space Grotesk',sans-serif;font-size:.7rem;
  letter-spacing:.2em;color:rgba(255,255,255,.25);right:0;top:6px
}

/* ── APP ── */
#app{position:relative;z-index:10;height:100vh;display:flex;flex-direction:column;display:none}

/* ── HDR ── */
.hdr{
  flex-shrink:0;height:56px;
  display:flex;align-items:center;justify-content:space-between;
  padding:0 1.2rem;
  border-bottom:1px solid var(--b1);
  background:rgba(5,5,10,.9);backdrop-filter:blur(20px)
}
.hdr-logo{
  font-family:'Space Grotesk',sans-serif;font-size:.85rem;font-weight:700;
  letter-spacing:.25em;text-transform:uppercase
}
.hdr-ping{
  display:flex;align-items:center;gap:.4rem;
  font-size:.6rem;font-weight:600;letter-spacing:.12em;color:var(--t2);text-transform:uppercase
}
.ping{width:6px;height:6px;border-radius:50%;background:var(--green);position:relative}
.ping::after{
  content:'';position:absolute;inset:-3px;border-radius:50%;
  background:var(--green);opacity:.4;animation:pingAnim 1.5s ease-out infinite
}
@keyframes pingAnim{0%{transform:scale(1);opacity:.5}100%{transform:scale(2.5);opacity:0}}

/* ── NAV ── */
.nav{
  flex-shrink:0;
  display:flex;padding:.5rem .8rem;gap:.25rem;
  border-bottom:1px solid var(--b1);overflow-x:auto;scrollbar-width:none;
  background:rgba(5,5,10,.7)
}
.nav::-webkit-scrollbar{display:none}
.n{
  flex-shrink:0;padding:.45rem 1rem;
  font-size:.68rem;font-weight:600;letter-spacing:.06em;text-transform:uppercase;
  color:var(--t3);cursor:pointer;transition:all .15s;
  border:1px solid transparent;border-radius:3px;
}
.n.on{color:var(--t1);border-color:var(--b2);background:var(--s1)}

/* ── SCROLL ── */
.scrl{flex:1;overflow-y:auto;overflow-x:hidden;scrollbar-width:none;-webkit-overflow-scrolling:touch}
.scrl::-webkit-scrollbar{display:none}

/* ── PAGE ── */
.pg{display:none;padding:1.4rem 1rem 5.5rem;animation:pgIn .25s ease}
.pg.on{display:block}
@keyframes pgIn{from{opacity:0;transform:translateY(8px)}to{opacity:1;transform:translateY(0)}}

/* ── TYPOGRAPHY ── */
.overline{
  font-size:.58rem;font-weight:700;letter-spacing:.3em;text-transform:uppercase;
  color:var(--t3);margin-bottom:.6rem;display:block
}
.display{
  font-family:'Space Grotesk',sans-serif;
  font-size:2.2rem;font-weight:700;line-height:1.05;letter-spacing:-.025em;
  margin-bottom:.8rem
}
.display span{color:var(--t2);font-weight:500}
.body-txt{font-size:.82rem;color:var(--t2);line-height:1.7;margin-bottom:1.4rem}

/* ── HERO BLOCK ── */
.hero{
  border:1px solid var(--b1);padding:1.6rem 1.4rem;margin-bottom:1.2rem;
  position:relative;overflow:hidden
}
.hero::after{
  content:'';position:absolute;top:0;left:0;right:0;height:1px;
  background:linear-gradient(90deg,transparent 0%,var(--a) 30%,rgba(80,70,229,.4) 70%,transparent 100%)
}
.metrics{display:grid;grid-template-columns:repeat(3,1fr);gap:.8rem;margin-top:1.4rem}
.metric{border-left:1px solid var(--b1);padding-left:.9rem}
.metric-v{font-family:'Space Grotesk',sans-serif;font-size:1.7rem;font-weight:700;letter-spacing:-.02em;line-height:1}
.metric-l{font-size:.6rem;font-weight:600;letter-spacing:.12em;text-transform:uppercase;color:var(--t3);margin-top:.3rem}

/* ── NUMBERED LIST (process) ── */
.proc{display:flex;flex-direction:column;border:1px solid var(--b1);margin-bottom:1.2rem}
.proc-item{
  display:flex;gap:1.2rem;padding:1.1rem 1.2rem;
  border-bottom:1px solid var(--b1);transition:background .15s;cursor:default
}
.proc-item:last-child{border-bottom:none}
.proc-item:hover{background:var(--s1)}
.proc-n{
  font-family:'Space Grotesk',sans-serif;font-size:.7rem;font-weight:700;
  color:var(--a);letter-spacing:.05em;flex-shrink:0;padding-top:.05rem;
  width:1.8rem
}
.proc-title{font-size:.85rem;font-weight:600;margin-bottom:.2rem}
.proc-desc{font-size:.73rem;color:var(--t2);line-height:1.6}

/* ── SERVICE LIST ── */
.svc-list{border:1px solid var(--b1);margin-bottom:1.2rem}
.svc{
  display:flex;align-items:center;gap:1rem;
  padding:1rem 1.2rem;border-bottom:1px solid var(--b1);
  cursor:pointer;transition:all .15s;position:relative
}
.svc:last-child{border-bottom:none}
.svc:hover,.svc:active{background:var(--s1)}
.svc::before{
  content:'';position:absolute;left:0;top:0;bottom:0;width:2px;
  background:var(--a);transform:scaleY(0);transform-origin:center;transition:.2s
}
.svc:hover::before{transform:scaleY(1)}
.svc-ico{
  width:36px;height:36px;flex-shrink:0;border:1px solid var(--b1);
  display:flex;align-items:center;justify-content:center
}
.svc-ico svg{width:16px;height:16px;stroke:var(--t2);fill:none;stroke-width:1.5;stroke-linecap:round;stroke-linejoin:round}
.svc-body{flex:1;min-width:0}
.svc-name{font-size:.82rem;font-weight:600;margin-bottom:.1rem}
.svc-desc{font-size:.68rem;color:var(--t3);white-space:nowrap;overflow:hidden;text-overflow:ellipsis}
.svc-right{text-align:right;flex-shrink:0}
.svc-price{font-size:.78rem;font-weight:700;white-space:nowrap}
.svc-time{font-size:.6rem;color:var(--t3);margin-top:.1rem}
.arr{color:var(--t3);margin-left:.4rem;font-size:.75rem}

/* ── CASES ── */
.case-list{display:flex;flex-direction:column;gap:.8rem;margin-bottom:1.2rem}
.case{border:1px solid var(--b1);padding:1.2rem}
.case-top{display:flex;justify-content:space-between;align-items:flex-start;margin-bottom:.8rem}
.case-title{font-size:.88rem;font-weight:700;margin-bottom:.15rem}
.case-sub{font-size:.63rem;color:var(--t3);letter-spacing:.05em;text-transform:uppercase}
.case-tag{
  font-size:.6rem;font-weight:700;letter-spacing:.08em;text-transform:uppercase;
  border:1px solid var(--a3);color:var(--a);padding:.15rem .6rem;flex-shrink:0;
  margin-left:.8rem
}
.case-body{font-size:.76rem;color:var(--t2);line-height:1.65;margin-bottom:.9rem}
.tags{display:flex;gap:.4rem;flex-wrap:wrap}
.tag{
  font-size:.62rem;font-weight:500;padding:.2rem .65rem;
  border:1px solid var(--b1);color:var(--t2)
}

/* ── PRICING ── */
.plan-list{display:flex;flex-direction:column;gap:.6rem;margin-bottom:1.2rem}
.plan{border:1px solid var(--b1);padding:1.2rem;cursor:pointer;transition:all .15s;position:relative}
.plan:hover,.plan:active{border-color:var(--b2);background:var(--s1)}
.plan.feat{border-color:var(--a3);background:var(--a2)}
.plan-badge{
  position:absolute;top:0;right:1.2rem;
  font-size:.58rem;font-weight:800;letter-spacing:.1em;text-transform:uppercase;
  background:var(--a);color:#fff;padding:.2rem .6rem;
  transform:translateY(-50%)
}
.plan-head{display:flex;justify-content:space-between;align-items:baseline;margin-bottom:.7rem}
.plan-name{font-size:.68rem;font-weight:700;letter-spacing:.18em;text-transform:uppercase;color:var(--t2)}
.plan-price{font-family:'Space Grotesk',sans-serif;font-size:1.5rem;font-weight:700;letter-spacing:-.02em}
.plan-sub{font-size:.65rem;color:var(--t3);margin-bottom:.9rem}
.plan-feats{list-style:none;display:flex;flex-direction:column;gap:.4rem;margin-bottom:.9rem}
.plan-feats li{font-size:.73rem;color:var(--t2);display:flex;gap:.6rem;align-items:flex-start}
.plan-feats li::before{content:'—';color:var(--a);font-weight:700;flex-shrink:0}
.plan-btn{
  width:100%;padding:.7rem;border:none;
  font-size:.76rem;font-weight:700;letter-spacing:.06em;text-transform:uppercase;
  font-family:'Inter',sans-serif;cursor:pointer;transition:opacity .15s
}
.plan-btn:active{opacity:.75}
.plan-btn-o{background:transparent;color:var(--t1);border:1px solid var(--b2)}
.plan-btn-f{background:var(--a);color:#fff}

/* ── DATA TABLE ── */
.dtable{border:1px solid var(--b1);margin-bottom:1.2rem}
.drow{display:flex;justify-content:space-between;align-items:center;padding:.7rem 1rem;border-bottom:1px solid var(--b1)}
.drow:last-child{border-bottom:none}
.dk{font-size:.7rem;color:var(--t3);font-weight:500;letter-spacing:.04em}
.dv{font-size:.7rem;font-weight:600}

/* ── CONTACT ── */
.contact-hero{
  border:1px solid var(--b1);padding:1.4rem 1.2rem;margin-bottom:.8rem
}
.contact-head{display:flex;gap:1rem;align-items:center;margin-bottom:1.2rem}
.contact-av{
  width:44px;height:44px;flex-shrink:0;
  background:var(--a2);border:1px solid var(--a3);
  display:flex;align-items:center;justify-content:center
}
.contact-av svg{width:20px;height:20px;stroke:#fff;fill:none;stroke-width:1.5;stroke-linecap:round;stroke-linejoin:round}
.contact-name{font-size:.95rem;font-weight:700;margin-bottom:.15rem}
.contact-sub{font-size:.65rem;color:var(--t3)}
.cbtn{
  width:100%;padding:.8rem;border:none;
  font-size:.78rem;font-weight:700;letter-spacing:.06em;text-transform:uppercase;
  font-family:'Inter',sans-serif;cursor:pointer;margin-bottom:.5rem;transition:opacity .15s
}
.cbtn:active{opacity:.75}
.cbtn-p{background:var(--a);color:#fff}
.cbtn-s{background:transparent;color:var(--t1);border:1px solid var(--b2)}

/* ── BOTTOM ── */
.bottom{
  position:fixed;bottom:0;left:0;right:0;z-index:50;
  padding:1rem 1rem 1.4rem;
  background:linear-gradient(to top,var(--bg) 60%,transparent)
}
.act{
  width:100%;padding:.9rem;border:none;
  background:var(--a);color:#fff;
  font-size:.82rem;font-weight:700;letter-spacing:.1em;text-transform:uppercase;
  font-family:'Inter',sans-serif;cursor:pointer;
  box-shadow:0 0 30px rgba(80,70,229,.25);transition:all .15s
}
.act:active{opacity:.8;transform:scale(.99)}

/* ── BIG NUMBER BG ── */
.bg-num{
  position:absolute;right:-1rem;top:50%;transform:translateY(-50%);
  font-family:'Space Grotesk',sans-serif;font-size:6rem;font-weight:900;
  color:rgba(255,255,255,.025);line-height:1;pointer-events:none;letter-spacing:-.05em;
  user-select:none
}
</style>
</head>
<body>
<canvas id="c"></canvas>

<!-- INTRO -->
<div id="intro">
  <div class="i-logo" id="iLogo">M8 CENTER</div>
  <div class="i-bar"><div class="i-bar-fill" id="iFill"></div><div class="i-num" id="iNum">0%</div></div>
</div>

<!-- APP -->
<div id="app">
  <div class="hdr">
    <div class="hdr-logo">M8 Center</div>
    <div class="hdr-ping"><div class="ping"></div><span id="hdrStatus">Online</span></div>
  </div>
  <div class="nav" id="nav"></div>
  <div class="scrl" id="scrl">
    <div id="pg-0" class="pg on"></div>
    <div id="pg-1" class="pg"></div>
    <div id="pg-2" class="pg"></div>
    <div id="pg-3" class="pg"></div>
    <div id="pg-4" class="pg"></div>
  </div>
  <div class="bottom"><button class="act" id="actBtn" onclick="cta()"></button></div>
</div>

<script>
// ════════════════════════════════════════════
// LANGUAGE DETECTION
// ════════════════════════════════════════════
const tg = window.Telegram?.WebApp;
if (tg) { tg.expand(); tg.setHeaderColor('#05050a'); tg.setBackgroundColor('#05050a'); }

const RU_LANGS = ['ru','uk','kk','uz','hy','az','ka','be','tg','tk','ky','mn'];
function detectLang() {
  const tgLang = tg?.initDataUnsafe?.user?.language_code || '';
  const navLang = (navigator.language || '').toLowerCase().slice(0, 2);
  const lang = (tgLang || navLang || 'en').toLowerCase().slice(0, 2);
  return RU_LANGS.includes(lang) ? 'ru' : 'en';
}
const LANG = detectLang();

// ════════════════════════════════════════════
// COPY
// ════════════════════════════════════════════
const T = {
  ru: {
    nav: ['Обзор','Услуги','Кейсы','Цены','Контакт'],
    status: 'Онлайн',
    cta: ['Начать проект','Запросить оценку','Обсудить кейс','Выбрать тариф','Написать нам'],
    hero: {
      over: 'AI Automation Agency',
      h1: 'Системы,\nкоторые',
      h2: 'работают\nза вас',
      p: 'Telegram-боты, AI-агенты и автоматизация на n8n. Клиентская коммуникация, продажи и операции — без участия человека.',
      m: [['500+','Проектов'],['48ч','Старт'],['24/7','Работа']],
    },
    proc: {
      over: 'Как это работает',
      items: [
        ['Опишите задачу','Пишите в Telegram — отвечаем в течение часа с уточнениями и предварительной оценкой.'],
        ['Получите расчёт','Конкретная цена, конкретная дата сдачи. 50% предоплата — работа начинается в течение 24 часов.'],
        ['Наблюдайте за прогрессом','Без чёрного ящика. Промежуточные результаты по мере разработки.'],
        ['Приёмка и поддержка','Полный исходный код, документация и 30–180 дней бесплатной поддержки.'],
      ]
    },
    svcs: {
      over: 'Что мы делаем',
      items: [
        ['Telegram-бот',        'Запись, продажи, CRM, оплата',          'от 22 000 ₽','3–7 дней','bot'],
        ['Автоматизация n8n',   'Интеграции, устранение ручного труда',  'от 35 000 ₽','5–10 дней','auto'],
        ['AI-агент',            'Claude/Gemini для продаж и поддержки',  'от 60 000 ₽','7–14 дней','ai'],
        ['3D-сайт',             'WebGL, GSAP — профессиональное присутствие','от 25 000 ₽','2–5 дней','web'],
        ['Инструменты WB/Ozon','Мониторинг цен, конкурентов, авто-ответы','от 30 000 ₽','4–7 дней','wb'],
        ['Контент AI',          'YouTube, Telegram, стратегия, скрипты', 'от 12 000 ₽','/ мес','cont'],
      ]
    },
    cases: {
      over: 'Реализованные проекты',
      items: [
        ['Автоматизация салона красоты','Бот записи · Google Calendar',
         'Автоматический поток записи клиентов, напоминания о визите, синхронизация расписания мастеров. Устранено 2 часа ежедневной ручной работы.',
         ['-2ч/день','0 пропущенных записей','35 000 ₽']],
        ['Система доставки еды','Бот заказов · Оплата · CRM',
         'Полное управление заказами внутри Telegram: меню, корзина, приём оплаты, уведомления кухне и курьеру.',
         ['+40% заказов','-60% времени обработки','55 000 ₽']],
        ['AI-агент для недвижимости','AI-агент · CRM · Follow-up',
         'Интеллектуальная квалификация входящих лидов, ведение истории переговоров, автоматические follow-up цепочки.',
         ['0 потерянных лидов','+35% конверсия','80 000 ₽']],
        ['Полная система агентства','10 AI-агентов · Память · Аналитика',
         'Директор, продажи, контент, юридический, финансовый агенты — с персистентной памятью, CRM и еженедельной аналитикой.',
         ['10 агентов','Работа 24/7','150 000 ₽']],
      ]
    },
    pricing: {
      over: 'Тарифные планы',
      plans: [
        ['Старт','22 000 ₽','до 35 000 ₽ · разово',
         ['Telegram-бот до 5 команд','Базовое меню и автоответы','Сдача за 3 дня','30 дней поддержки'],false,'starter'],
        ['Профессионал','40 000 ₽','до 70 000 ₽ · разово',
         ['Полноценный бот с базой данных','CRM или Google Таблицы','Платёжная система','Сдача за 7 дней','90 дней поддержки'],true,'pro'],
        ['Корпоративный','120 000 ₽','до 200 000 ₽ · разово',
         ['Полная AI-система (10 агентов)','Память, CRM, аналитика','Автоматизация продаж и контента','180 дней поддержки'],false,'ent'],
      ],
      terms: [['Предоплата','50%'],['Остаток','После сдачи'],['Оплата','Банк · Крипто'],['Исходный код','Передаётся'],['NDA','По запросу']]
    },
    contact: {
      over: 'Контакты',
      role: 'AI Automation Agency · Отвечаем в течение часа',
      btn1: 'Написать в Telegram',
      btn2: 'Реквизиты',
      req: 'Альфа Банк\n+7 901 772 55 91\nРустамов Ахмаджон Юнусович\n\nПредоплата 50% до начала работ.',
      table: [['Банк','Альфа Банк'],['Телефон','+7 901 772 55 91'],['NDA','Доступен'],['Договор','По запросу'],['Самозанятый','Есть чеки']]
    }
  },
  en: {
    nav: ['Overview','Services','Cases','Pricing','Contact'],
    status: 'Online',
    cta: ['Start a project','Get a quote','Discuss a case','Choose a plan','Write to us'],
    hero: {
      over: 'AI Automation Agency',
      h1: 'Systems that',
      h2: 'work\nfor you',
      p: 'Telegram bots, AI agents, and n8n automation. Client communication, sales, and operations — without manual involvement.',
      m: [['500+','Projects'],['48h','Start time'],['24/7','Uptime']],
    },
    proc: {
      over: 'How it works',
      items: [
        ['Describe your task','Write to us in Telegram — we reply within the hour with questions and an initial estimate.'],
        ['Receive a clear quote','Exact price, exact delivery date. 50% upfront — work starts within 24 hours.'],
        ['See progress','No black box. Intermediate deliverables throughout development.'],
        ['Delivery and support','Full source code, documentation, 30–180 days of free support.'],
      ]
    },
    svcs: {
      over: 'What we build',
      items: [
        ['Telegram Bot',       'Bookings, sales, CRM, payments',        'from $300','3–7 days','bot'],
        ['n8n Automation',     'Tool connections, process elimination',  'from $500','5–10 days','auto'],
        ['AI Agent System',    'Claude/Gemini for sales and support',   'from $800','7–14 days','ai'],
        ['3D Website',         'WebGL, GSAP — premium web presence',    'from $400','2–5 days','web'],
        ['WB / Ozon Tools',    'Price monitor, competitor, auto-reply', 'from $400','4–7 days','wb'],
        ['AI Content',         'YouTube, Telegram, strategy, scripts',  'from $150','/ month','cont'],
      ]
    },
    cases: {
      over: 'Selected projects',
      items: [
        ['Beauty Salon Automation','Booking Bot · Google Calendar',
         'Automated booking flow with reminders and master schedule sync. Eliminated 2 hours of daily manual work.',
         ['-2h / day','0 missed bookings','$350']],
        ['Food Delivery System','Order Bot · Payments · CRM',
         'Full order management inside Telegram: menu, cart, payment, kitchen and courier notifications.',
         ['+40% orders','-60% processing time','$600']],
        ['Real Estate AI Agent','AI Agent · CRM · Follow-up',
         'Smart lead qualification, persistent conversation memory, automated multi-step follow-up sequences.',
         ['0 lost leads','+35% conversion','$900']],
        ['Agency Automation Stack','10 AI Agents · Memory · Analytics',
         'Director, sales, content, legal, finance agents — with persistent memory, CRM, and weekly analytics.',
         ['10 agents','24/7 operation','$1 800']],
