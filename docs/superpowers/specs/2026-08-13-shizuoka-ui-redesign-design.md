# 靜岡旅遊行程頁 UI 全面重新設計

- Date: 2026-08-13
- Slug: `shizuoka-ui-redesign`
- Target: `2025-10-04-10-12-tokyo/` (index.html, omiyage.html, credit-cashback.html)

## 1. Request

User request, verbatim:

> 幫我優化靜岡旅遊行程的UI，不用管以前的UI如何，用你覺得最好最適合的方式重新設計

Clarified in Phase 1:

- **Scope**: all three pages in the folder get the redesign and share one new visual
  language (user chose 「三頁全改，統一新風格」).
- **Content**: restructuring and trimming redundant wording is permitted
  (user chose 「可順手重整結構與精簡贅字」).

## 2. Current state

`2025-10-04-10-12-tokyo/index.html` — 2,891 lines / 129 KB, single self-contained
file, `lang="zh-TW"`, no build step (served as static GitHub Pages).

Content inventory (must all survive the redesign):

| Item | Count | Notes |
| --- | --- | --- |
| Day cards | 9 (`#day1`–`#day9`) | 10/04–10/12 |
| Overview card | 1 (`#summary`) | 2 flights + 9-row trip outline |
| Timeline stops | 93 | each = time + title + optional `.transport` + optional `.note` |
| Background/info boxes | 24 `.info-card` | long 景點/歷史背景 prose |
| Tables | 4 | train alternatives (N'EX, etc.) |
| 相關連結 blocks | 9 | official sites + Google Maps |
| 住宿資訊 blocks | 8 | hotel name, price, breakfast |
| Local images | 2 | `image/nex_ticket_qrcode.png`, `image/新宿御苑 スタンプ設置箇所.png` |
| Inline `style="…"` | 85 | to be eliminated |

Behaviour to preserve: collapsible day cards, auto-expand the card matching
today's `MM/DD` (falling back to expanding 行程總覽 when no day matches — the trip
is in the past, so this is the normal path), back-to-top button, in-page nav,
links to `omiyage.html` and `credit-cashback.html`.

Sub-pages: `omiyage.html` (7 shop cards across 橫濱/靜岡) and
`credit-cashback.html` (富邦J卡 detail + 玉山 two cards as remote images +
使用建議). Both currently use the same `#667eea→#764ba2` purple gradient template.

Problems: three unrelated visual styles across one trip; olive/beige palette with
no connection to the trip's subject; 93 stops rendered as an undifferentiated
dot-timeline so times and warnings do not stand out; times set in body text (not
tabular) making the itinerary hard to scan on a phone; 85 inline styles; no link
back to the site root (`../index.html`), unlike newer trip pages.

## 3. Design direction — 「駿河三十六景」

### 3.1 Thesis

This trip is a circuit *around Mount Fuji*: 熱海 on its east flank, 富士宮 at its
foot, 日本平 looking back across 駿河灣, down 伊豆 and out through 三島. Fuji is the
one fixed reference point across all nine days. The defining artifact of that
subject is Hokusai's 冨嶽三十六景 — a *numbered series of views* of the same
mountain. So the page is built as a series of views: nine days, each a 景, with the
mountain as the recurring anchor.

Rejected as generic defaults: cream + high-contrast Latin serif + terracotta;
near-black + single acid accent; broadsheet hairline columns. The ground here is
cool-grey washi, the dominant colour is Prussian blue at full bleed, and the
display face is 明朝体 rather than a Latin serif.

### 3.2 Color

Derived from ukiyo-e woodblock pigments, not from a generic palette:

| Token | Hex | Role |
| --- | --- | --- |
| `--bero` | `#1B4C7E` | ベロ藍 Prussian blue — the imported pigment Hokusai's Fuji series is famous for. Primary structural colour: hero, day headers, links. |
| `--bero-deep` | `#0E2B47` | Deepest indigo — hero base, footer. |
| `--nami` | `#7FA9C9` | 波 mid-tone blue — print gradient steps, rules, inactive nav. |
| `--washi` | `#E9E6DD` | Cool grey-cream paper ground — page background. |
| `--washi-hi` | `#F6F4EF` | Raised card surface. |
| `--sumi` | `#14181A` | 墨 key-block ink — body text. |
| `--beni` | `#A83B32` | 紅 vermilion — the ONE accent. Reserved for time-critical items only (e.g. the 16:30 駿府城 stamp deadline) and the Fuji summit mark. |
| `--cha` | `#55633A` | 茶 tea green — secondary, food/tea items only. |
| `--sumi-mute` | `#4A5560` | Muted ink — secondary text: inactive rail labels, captions, `.note` body. |
| `--kasumi` | `#C8DEEF` | 霞 haze — secondary text **on the dark hero and footer** (hero nav, subtitle, facts, day numeral label). |
| `--beni-lite` | `#FAD2CD` | The Fuji mark when it sits on a dark ground (the hero's 富士見 counter). |
| `--yu` | `#7A5230` | 湯 — ground for the onsen chip. |
| `--murasaki` | `#5A4A7A` | 買 — ground for the shopping chip. |
| `--wash` / `--wash-firm` | `rgba(27,76,126,.05)` / `.09` | Pale blue washes for raised panels, table headers and captions. |
| `--beni-wash` | `rgba(168,59,50,.08)` | Wash behind a booked table row or an urgent time chip. |

Full-bleed blue carries the page so the cream ground never dominates.

**Contrast, measured (WCAG 2.1 relative luminance), not estimated:**

| Foreground | on `--washi` `#E9E6DD` | on `--washi-hi` `#F6F4EF` |
| --- | --- | --- |
| `--sumi` | 14.32:1 | 16.26:1 |
| `--bero` | 7.07:1 | 8.02:1 |
| `--sumi-mute` | 6.10:1 | 6.92:1 |
| `--cha` | 5.21:1 | 5.91:1 |
| `--beni` | 5.04:1 | 5.73:1 |

On the **dark** grounds, measured against the hero gradient's three stops
(`--bero-deep` → `--bero` → `#27618F`) and the solid `--bero-deep` footer:

| Foreground | worst of the three stops | on `--bero-deep` |
| --- | --- | --- |
| `--washi-hi` | 5.98:1 | 13.13:1 |
| `--kasumi` | **4.75:1** | 10.41:1 |
| `--beni-lite` | **4.75:1** | 10.42:1 |

Both `--kasumi` and `--beni-lite` were darkened/lightened to reach 4.5:1 against the
gradient's lightest stop rather than only against `--bero-deep`, so hero text stays
compliant wherever it falls.

Chip grounds carry `--washi-hi` text: `--bero` 8.02:1 · `--bero-deep` 13.13:1 ·
`--cha` 5.91:1 · `--yu` 6.21:1 · `--murasaki` 7.09:1.

**Text-bearing tokens** are exactly `--sumi`, `--sumi-mute`, `--bero`, `--cha`,
`--beni` (on light grounds) and `--washi-hi`, `--kasumi`, `--beni-lite` (on dark
grounds). Every one clears 4.5:1 against every ground it is actually used on.

`--nami` (2.00:1 on washi) is a **non-text token** — permitted only for rules,
ridge/wave fills, gradient steps and borders, **never for a glyph, on any
background**. This is absolute, so the hero navigation uses `--kasumi` rather than
`--nami`, inactive rail labels use `--sumi-mute`, and the `.leg` connector is a real
`border-top` rather than a `──` text glyph.

Earlier drafts of this spec used `#B4453C` for `--beni` and `#5C6B3E` for `--cha`;
both measured below or uncomfortably near 4.5:1 on `--washi` (4.36:1 and 4.64:1) and
were darkened to the values above.

### 3.3 Type

Three roles, system fonts only (no webfont — the page must work offline on the road):

- **Display** — `"Hiragino Mincho ProN", "Yu Mincho", "Songti TC", "Noto Serif TC", serif`.
  Real 明朝体 on the user's Mac/iPhone. Used with restraint: hero title, 漢数字 day
  numerals, day route lines.
- **Body** — `"PingFang TC", "Hiragino Sans", "Noto Sans TC", system-ui, sans-serif`.
  Traditional-Chinese-first so 繁中 glyphs are correct, with Hiragino Sans catching
  the Japanese strings (ホテルプリヴェ静岡, バリ勝男クン, …).
- **Data** — `ui-monospace, "SF Mono", "Roboto Mono", monospace` with
  `font-variant-numeric: tabular-nums`. All times, prices, train numbers, and
  reservation codes. Times are the most-scanned data on the page, so they get their
  own face and align in a column.

Type scale (rem, 1rem = 16px): 0.72 / 0.8 / 0.9 / 1 / 1.15 / 1.45 / 1.9 / 2.6 / 4.
Hero title uses `clamp()`.

### 3.4 Layout

```
┌────────────────────────────────────────────────┐
│ ████ full-bleed --bero-deep → --bero hero      │
│                                                │
│  駿河三十六景                                   │
│  靜岡・伊豆・鎌倉  九日八夜                     │
│  2025.10.04 — 10.12   阿宅阿婷                  │
│                                                │
│   ╱╲    layered ukiyo-e ridge + wave SVG       │
│  ╱  ╲___╱╲______  (page-load reveal)           │
└────────────────────────────────────────────────┘
┌ sticky rail ───────────────────────────────────┐
│ 総覽 │ 一 │ 二 │ 三 │ 四 │ 五 │ 六 │ 七 │ 八 │ 九 │
└────────────────────────────────────────────────┘
┌────────────────────────────────────────────────┐
│ ┃二┃  10/05　週日        [鎌倉][熱海][靜岡]    │  ← 漢数字, vertical
│ ┃景┃  大船 → 熱海 → 靜岡・駿府城公園・歷史博物館 │
├───────┬────────────────────────────────────────┤
│15:00左右│ 移 從熱海前往靜岡                      │
│ (mono)│    ── JR東海道本線 普通車 (約 1小時20分) │
│       │    ▸ (使用Pass) 開始使用周遊券 Mini      │
├───────┼────────────────────────────────────────┤
│ 16:25 │ 見 衝刺【駿府城公園】蓋章       ▲ 富士見 │
│ -16:30│    ── 快速前往東御門券賣所               │
│       │    ⚠ 僅剩5分鐘！優先目標：蓋百名城章     │  ← --beni
├───────┴────────────────────────────────────────┤
│ 宿  ホテルプリヴェ静岡   18,700日圓  不含早餐    │
│ 連結  官網 · 地圖                               │
└────────────────────────────────────────────────┘
```

(Day 2 is used here because it carries both a 富士見 stop and the trip's one
genuinely time-critical moment, so it exercises every device at once.)

- Container `max-width: 68rem`, generous vertical rhythm.
- **Stop rows are a two-column grid**: fixed left gutter for the time (tabular
  mono, right-aligned), flexible right column for content. This replaces the
  dot-timeline and is the main scannability win. Below 640px the time becomes a
  small chip above the content and the grid collapses to one column.
- Day header shows the day's route line and region tag(s).

**Sticky day rail at narrow widths.** The rail has 10 items (総覽 + 九日). At a 44px
minimum touch target that needs ≥440px, which does not fit 320px. Resolution:

- The rail is a **deliberately horizontally-scrollable strip**: `overflow-x: auto`,
  `scroll-snap-type: x proximity`, `-webkit-overflow-scrolling: touch`, no wrapping.
- Below 640px each item collapses to its compact form — the single glyph (`総`,
  `一`…`九`) with the date hidden — sized 44×44px. Ten of these plus gaps ≈ 480px, so
  roughly seven are visible at 320px and the rest are reached by swiping.
- A gradient fade on both edges signals the overflow, and the active item is scrolled
  into view via `scrollIntoView({ block: 'nearest', inline: 'center' })` when the
  `IntersectionObserver` changes the active day.
- **Definition:** the 「no horizontal scroll」 requirement in §4 applies to the
  document only — `document.documentElement.scrollWidth` must equal its
  `clientWidth` at every tested width. The rail is the single intentional exception:
  it is an internally scrolling element and does not widen the page. No other element
  may overflow horizontally.

### 3.5 Structural devices (each encodes something true)

- **漢数字 day numerals** (一…九) set large in Mincho, `writing-mode: vertical-rl`
  on the day header. Numbering is legitimate: the days are a real sequence, and the
  series concept is literally numbered views. Vertical setting is the one aesthetic
  risk of the design — it is authentic to Japanese print, striking, and falls back
  to horizontal below 640px.
- **Kanji kind-chips** on each stop, replacing the current leading emoji:
  移 (移動) · 見 (見学) · 食 (食事) · 宿 (宿泊) · 湯 (温泉) · 買 (買物). One character,
  derived from what the stop actually is. This is a deliberate, reversible taste call
  — see §6.

  All 84 day stops are classified from the **stop title only**. Notes are advisory and
  never determine the chip — this is load-bearing: 「參觀【久能山東照宮】」 whose note
  mentions 「可在此附近用餐」 is a shrine visit (見), not a meal, and a transit leg
  whose destination happens to be 「…溫泉」 or 「…公園」 is still 移.

  **Ordered rule, first match wins:**

  | # | Test on the title | Result |
  | --- | --- | --- |
  | 1 | contains 入住 or 辦理住宿 | 宿 |
  | 2 | contains 飯店/ホテル/酒店 **and** 休息 | 宿 |
  | 3 | contains 溫泉區 / 入湯 / 足湯 | 湯 |
  | 4 | begins with an activity verb (ignoring a leading 【 or ⭐️): 參觀 · 參拜 · 探訪 · 漫步 · 登上 · 體驗 · 衝刺 · 自由逛逛 · 逛逛 · 最後的 | → **activity test** |
  | 5 | contains a movement verb: 搭乘 · 搭車 · 搭船 · 搭公車 · 前往 · 返回 · 回程 · 出發 · 抵達 · 入境 · 退房 · 轉乘 · 寄放 · 寄行李 · 取回 · 登機 | 移 |
  | 6 | contains `→` with no 【 before it (a bare `A → B` leg) | 移 |
  | 7 | otherwise | → **activity test** |

  **Activity test** — of these keyword groups, whichever keyword occurs *earliest* in
  the title wins; if none occur, the stop gets **no chip** and the layout must not
  assume one is present:

  | Chip | Keywords |
  | --- | --- |
  | 食 | 早餐 · 午餐 · 晚餐 · 用餐 · 餐廳 · 品嚐 · 咖啡 |
  | 買 | 購物 · 逛街 · 商店街 · 百貨 · 採購 · 伴手禮 · 藥妝 · 免稅 · 逛 |
  | 見 | 參觀 · 參拜 · 探訪 · 漫步 · 登上 · 體驗 · 衝刺 · 蓋章 · 神社 · 大社 · 寺 · 城 · 公園 · 博物館 · 美術館 · 展望 · 瀑布 · 樂園 · 步道 · 夕陽 · 夜景 · 燈塔 · 苑 · 湧水 · 岩屋 · 大佛 · 天空 |

  Worked examples: 「飯店入住」→ 宿 (1); 「返回飯店休息」→ 宿 (2); 「參觀【修善寺溫泉區
  景點】」→ 湯 (3, before the activity test); 「參拜【中津宮 → 奧津宮】」→ 見 (4, so the
  arrow in rule 6 never fires); 「飯店退房，從大船前往熱海」→ 移 (5); 「山下公園→みなと
  みらい」→ 移 (6, the 公園 in the station name is ignored); 「最後的採購與午餐」→ 買
  (7 → activity, 採購 precedes 午餐).

  This rule is **mechanically verifiable and produces zero exceptions** across all 84
  stops (see §8.1).

- **Region tags** per day. Vocabulary (9 values): 東京 / 千葉 / 橫濱 / 鎌倉 / 熱海 /
  靜岡 / 富士 / 伊豆 / 三島. A day is tagged with every distinct region it actually
  *visits*, in itinerary order, capped at 3 — passing through a region by train does
  not earn a tag. 成田機場 is in **千葉**, not 東京, so day 1 (land at Narita, ride
  straight to Ofuna) never touches Tokyo, while day 9 visits 品川 and 新宿 (東京)
  before departing from Narita (千葉). Derived from the existing 行程總覽 route lines:

  | Day | Route line | Region tags |
  | --- | --- | --- |
  | 一 10/04 | 抵達成田 → N'EX → 大船 | 千葉・鎌倉 |
  | 二 10/05 | 大船 → 熱海 → 靜岡 | 鎌倉・熱海・靜岡 |
  | 三 10/06 | 靜岡 → 日本平 → 久能山 | 靜岡 |
  | 四 10/07 | 靜岡 → 富士 → 富士宮 | 靜岡・富士 |
  | 五 10/08 | 靜岡 → 清水 → 土肥 → 修善寺 | 靜岡・伊豆 |
  | 六 10/09 | 修善寺 → 三島 → 靜岡 | 伊豆・三島・靜岡 |
  | 七 10/10 | 靜岡 → 大船 → 鎌倉 | 靜岡・鎌倉 |
  | 八 10/11 | 大船 → 橫濱 | 鎌倉・橫濱 |
  | 九 10/12 | 橫濱 → 品川 → 東京 → 成田 | 橫濱・東京・千葉 |

  A stop whose location differs from its day's tags needs no per-stop tag — tags are
  a day-level device only, so cross-region days are handled by simply listing both.
- **富士見 mark** — a small vermilion Fuji glyph on stops where Fuji is genuinely
  visible on the page's own evidence. Seven stops qualify:

  | Day | Stop | Evidence |
  | --- | --- | --- |
  | 二 | 駿府城公園 | 背景：「從這裡可眺望富士山」 |
  | 三 | 日本平夢テラス | 「欣賞富士山、駿河灣絕景」 |
  | 三 | 久能山東照宮 | 同一山稜的展望點，背景述及富士 |
  | 四 | 靜岡縣富士山世界遺產中心 | 「登螺旋坡到屋頂看富士山」 |
  | 四 | 白糸の滝 | 富士山湧水瀑布群，正面望富士 |
  | 六 | 三島天空步道 | 「360度欣賞富士山」 |
  | 七 | 長谷寺 | 背景：「見晴台可遠眺江之島和富士山」 |

  The hero counter therefore reads 「**富士見 七景**」. **This is the signature
  element** — it ties the 三十六景 concept to real itinerary data, so the theme is
  informational rather than decorative.
- **Note severity**, replacing today's uniform grey italics: `▸` neutral tip ·
  `⚠` vermilion time-critical/warning · `¥` cost.

  Classified by a **top-down, first-match decision list** on the note's own text:

  | # | Trigger | Class | Marker / colour |
  | --- | --- | --- | --- |
  | 1 | ⚠️ / 緊急 / 僅剩 / 務必 / 關門**前** / 最晚 | warning | `⚠` `--beni` |
  | 2 | 不使用Pass / 不能使用Pass / 需另外購買 / 需另外購票 / 自費 / 門票 / 車資 / `¥` | cost | `¥` `--cha` |
  | 3 | anything else | neutral | `▸` `--sumi-mute` |

  **Mixed content.** Severity outranks category: a note matching both rules 1 and 2
  renders as a warning. Where the original note text contains sentences of *differing*
  severity, it may be split at sentence boundaries into one note per class,
  preserving each sentence verbatim and in its original order. Two constraints on
  splitting:

  - never split mid-sentence;
  - a `(可使用Pass)` / `(不使用Pass)` / `(使用Pass)` qualifier always stays attached
    to the sentence it qualifies, and never migrates to an adjacent note.

  The literal `⚠️` emoji is dropped from note text where rule 1 fires, since the `⚠`
  marker and vermilion colour now carry that meaning (consistent with the emoji rule
  in §5).

  The trigger is 關門**前** (before closing), not 關門 alone. 「券賣所16:30關門後，仍可
  在公園內參觀…」 tells the reader what remains *possible* after closing — it is
  reassurance, not a deadline, and stays neutral. Marking it red would misreport the
  one genuinely urgent moment of the trip.

  A stop whose note is a warning also gets `.stop--urgent`, which tints its time in
  `--beni` so the deadline is visible without reading the note.

### 3.6 Motion

Deliberately minimal:

1. Hero ridge layers rise in sequence on load (~700ms, staggered).
2. Sticky rail marks the active day via `IntersectionObserver`.
3. Day expand/collapse is native `<details>`/`<summary>` (see §4). Closed content is
   removed from layout entirely, so the old `max-height: 5000px` clipping bug cannot
   occur and there is no row size to animate between. Opening plays a short
   fade/slide (`@keyframes reveal`, ~300ms) on the panel instead.

Nothing else animates. All of the above is disabled under
`prefers-reduced-motion: reduce`.

### 3.7 Sub-pages

Same tokens, same hero pattern, one shared inline stylesheet copied into each file
(no shared CSS file — each page stays self-contained, matching the repo convention).

- `omiyage.html` — 土産 catalogue. Shops grouped by city (橫濱 / 靜岡), each a card
  with name, description, and a definition-list of 營業時間 / 交通 / 地址+地圖. The
  three 靜岡 entries have no logistics data; their cards render description-only
  rather than showing empty rows.
- `credit-cashback.html` — 回饋 by bank. 富邦J卡's two 回饋 tiers become two clearly
  labelled blocks with the headline rate in display type and the terms as an ordered
  list. 玉山's two remote images keep `loading="lazy"` and explicit alt text.

## 4. Technical requirements

- Three self-contained static HTML files. No build step, no external CSS/JS, no
  webfonts, no CDN dependency. `lang="zh-TW"`, UTF-8.
- **Design tokens** — every colour, font stack, and spacing step is declared once as a
  custom property in `:root` (the `--bero`/`--washi`/`--sumi`… palette of §3.2, the
  three `--display`/`--body`/`--data` stacks of §3.3, and a `--s1`…`--s8` spacing
  scale) and referenced via `var()` everywhere else. No raw hex colour, font-family
  list, or spacing value may be repeated in a rule body.
  Exempt, because they are structural rather than tokens: grid/flex templates,
  `font-size` values (which use a `clamp()` scale), SVG path data, `border-radius`,
  z-index, animation timings, and the gradient stop `#27618F` plus the ridge fills
  `#4E7FA6`/`#EAF2F8`/`#123A5C`/`#164368`, which exist only inside the hero artwork.
- **Zero inline `style` attributes** in the final markup (down from 85).
- Semantic HTML: `<header>`, `<nav>`, `<main>`, `<section>`, `<footer>`; day
  collapse built on `<details>`/`<summary>` so it works with JS disabled and is
  keyboard-accessible for free.
- Accessibility floor: every text colour ≥4.5:1 against its actual background per the
  §3.2 tables, with `--nami`/`--bero-deep` never used for text; visible
  `:focus-visible` rings; **≥44px touch targets for every standalone control** — rail
  links, day summaries, background-block summaries, quick links, link-list entries,
  footer and hero nav links, the back-to-top button, and any link that is the sole
  content of a `.note` (since `.note` is a flex container, such a link blockifies onto
  its own row and is a standalone control in practice).
  *Exempt:* links inline within a sentence of prose, per WCAG 2.5.8 Target Size
  (Minimum), which excludes targets "in a sentence or block of text". Enlarging those
  would break the line box.
- Reduced motion must be honoured in **JavaScript as well as CSS**: an explicit
  `behavior: 'smooth'` in `scrollTo`/`scrollIntoView` overrides
  `scroll-behavior: auto`, so both call sites read
  `matchMedia('(prefers-reduced-motion: reduce)')` and pass `'auto'` when it matches.
  `prefers-reduced-motion` respected; images have real `alt`; nav is a real list;
  the ridge SVG is `aria-hidden`.
- Responsive: verified at 320 / 390 / 768 / 1280px.
- Add a 「← 所有行程」 link back to `../index.html` on all three pages (repo
  convention, currently missing here).
- Keep all existing anchor ids (`#summary`, `#day1`…`#day9`) so old links and
  bookmarks still resolve.
- Keep every existing link target and image source byte-identical (see §5 for the
  exact inventory), with the **single exception of the one malformed URL listed in
  §5.1**. New link targets may be *added* (§5), but none may be dropped, and none
  altered apart from that one exception.

## 5. Content-handling rules

Restructuring is permitted, but factual data is inviolable.

**Hard invariants — zero loss:**

- Every time, price, flight/train number, reservation code
  (`20342479764521208`), hotel name, station name, place name, and opening hour.
- **Link targets (`href`)** — every existing one survives, except the single
  malformed value corrected in §5.1. Current inventory of unique values:

  | File | External `http(s)` | In-page `#` anchors | Relative |
  | --- | --- | --- | --- |
  | `index.html` | 58 | 10 (`#summary`, `#day1`–`#day9`) | 3 (`omiyage.html`, `credit-cashback.html`, `image/nex_ticket_qrcode.png`) |
  | `omiyage.html` | 4 | 0 | 1 (`index.html`) |
  | `credit-cashback.html` | 1 | 0 | 1 (`index.html`) |

  Note `image/nex_ticket_qrcode.png` appears as an `href` too — the QR image is
  wrapped in a link so it can be opened full-size. That wrapper is preserved.
- **Image sources (`src`)** — verified separately from `href`, since the two sets are
  distinct:

  | File | `<img src>` | Values |
  | --- | --- | --- |
  | `index.html` | 2 | `image/nex_ticket_qrcode.png`, `image/新宿御苑 スタンプ設置箇所.png` |
  | `omiyage.html` | 0 | — |
  | `credit-cashback.html` | 2 | two `https://github.com/user-attachments/...` URLs |

- Every 歷史背景 / 景點介紹 paragraph — kept in full. These are the user's own
  writing and the reason the page is worth keeping; they are re-housed in a
  collapsible 「背景」 block, not trimmed.
- All 4 alternative-train tables, including which row is 已預約.

### 5.1 The one permitted `href` correction

Exactly one existing link target changes value. Day 7's 鐮倉大佛地圖 link is malformed
in the source — unbalanced quoting swallowed the `target` attribute into the URL:

```html
<a _blank"="" href="https://maps.app.goo.gl/wqHn1GF9DnGSzHBG7 target=">
```

| | Value | HTTP | Resolves to |
| --- | --- | --- | --- |
| Before | `https://maps.app.goo.gl/wqHn1GF9DnGSzHBG7 target=` | **400** | — |
| After | `https://maps.app.goo.gl/wqHn1GF9DnGSzHBG7` | **200** | `鎌倉大佛殿高德院` (Kotoku-in) |

Both were checked with `curl` before the change was applied; the destination matches
the link's own label, confirming the trailing `target=` was the only defect. This is
the **only** old→new `href` difference permitted in any of the three files.

**Permitted additions** (the only `href` values that may be new):

- `../index.html` — the 「← 所有行程」 root link, added to all three pages (§4).
- On the sub-pages, the shared nav may link to the other two pages of this trip
  (`index.html`, `omiyage.html`, `credit-cashback.html`).

No other new link targets. Nothing existing is dropped or altered.

**Permitted changes:**

- Re-house repeated 住宿資訊 / 相關連結 into one consistent block per day.
- Drop purely decorative **leading** emoji in favour of the design's own glyph system,
  in two places:
  - **Stop titles** — the leading ✈️/🏨/🍽️/🏯/🎯 etc. are replaced by the kanji chip
    (§3.5).
  - **Link labels** — the 36 leading `📍` and 25 leading `🌐` markers become the
    `地` / `網` display-face glyphs of `.linklist .gl`, which encode the same
    distinction (map vs official site) in the page's own typographic voice.

  Emoji that sit *inside* prose, or that head a background block's title
  (🌊 溫泉度假勝地, 👑 德川家康居城, …), are the user's voice and stay untouched.
- Trim duplicated connective wording (e.g. a stop title that repeats the day
  header's route verbatim).
- Normalise mixed 靜岡/静岡 usage in *prose* to 靜岡 (zh-TW), while proper nouns keep
  their Japanese spelling (ホテルプリヴェ静岡, 日本平夢テラス).

## 6. Explicitly reversible decisions

Flagged for the human gate — each can be dropped without touching the rest:

1. **Kanji kind-chips replacing leading emoji.** Cleaner and more distinctive, but
   it changes the page's voice. Reverting means putting the emoji back in the titles.
2. **Vertical 漢数字 day numerals.** The design's one risk. Reverting means setting
   them horizontally.
3. **No dark mode.** A woodblock print is a print; a night variant is off-concept
   and doubles the CSS surface. Out of scope for this run.

## 6a. Amendment (2026-08-13, during implementation)

Three deviations from the spec as approved. Each was found while implementing and
is recorded here so the spec matches what was built.

### A1 — One broken link target is corrected

Day 7's 鐮倉大佛地圖 `href` is malformed in the source and is corrected. Resolved into
the body of the spec rather than left as an exception here: see **§5.1** for the
before/after values and the `curl` verification, **§4** and **§5** for the amended
invariant, and **§8.1** for the exact old→new difference the parity check accepts.

The user initially chose to keep the broken URL as-is, then asked for it to be fixed
provided the fix could be verified. It was verified (400 → 200, resolving to
`鎌倉大佛殿高德院`), so the correction stands.

### A2 — 富士見 marks: seven stops, not six

§3.5's criterion is "stops where Fuji is genuinely visible", illustrated with a
six-item list. 駿府城公園 (day 2) also qualifies on the page's own evidence — its
background essay states 「從這裡可眺望富士山」 — so it carries a mark too. The marked
set is seven:

駿府城公園 · 日本平夢テラス · 久能山東照宮 · 靜岡縣富士山世界遺產中心 ·
白糸の滝 · 三島天空步道 · 長谷寺

The hero counter reads 「富士見 七景」 accordingly. The criterion is unchanged; only
the enumeration was incomplete.

### A3 — Day collapse uses native `<details>`, not a `grid-template-rows` animation

The spec as first approved was self-contradictory: §3.6.3 specified animating
`grid-template-rows: 0fr → 1fr`, while §4 independently required
`<details>`/`<summary>`. Native `<details>` removes closed content from layout
entirely, so the `max-height: 5000px` clipping bug that motivated the grid technique
cannot occur, and there is no row size to animate between.

§4 wins — working without JS and being keyboard-accessible for free is worth more than
the transition. **§3.6.3 has been rewritten** accordingly; a short fade/slide
(`@keyframes reveal`) plays on open, still disabled under `prefers-reduced-motion`.

## 6b. Amendment 2 (2026-08-13, after code review)

Codex's code review raised nine findings. Six were straight implementation defects and
were fixed in code with no spec change: `--nami` used for glyph colour on three pages,
unapproved raw hex literals, `behavior:'smooth'` ignoring reduced motion, sub-44px
controls, sub-page `<nav>`s that were not real lists, and the 【…】 emphasis brackets
dropped from `credit-cashback.html` (restored).

Three required the spec itself to change, because the spec — not the page — was wrong:

### A4 — The chip decision list is rewritten (§3.5)

The originally approved list keyed off "the stop's own title **and note text**" with a
flat keyword precedence. Re-deriving all 84 chips from it produced **30 mismatches**,
and inspection showed the rule was at fault in the great majority: a note reading
「可在此附近用餐」 turned a shrine visit into 食, a bus ride to 「…修善寺溫泉」 became 湯,
and a transit leg through 「山下公園」 became 見. The keyword lists were also incomplete
(返回, 搭車, 逛街, 步道, 夜景 … all unmatched).

§3.5 is replaced with an ordered, **title-only** rule that separates movement legs from
activities and ranks multi-match titles by earliest keyword. It re-derives all 84 chips
with **0 mismatches**. Three chips were corrected in the page to agree with it
(自由逛逛熱海市區 → 買, 參觀【橫濱紅磚倉庫2號館】 → 見,
【横浜ランドマークタワー…】準備看夜景 → 見).

### A5 — Note-severity trigger narrowed to 關門前 (§3.5)

`關門` alone would classify 「券賣所16:30關門後，仍可在公園內參觀…」 as a warning and tint
its time vermilion. That sentence is reassurance about what remains possible *after*
closing, not a deadline; marking it red would dilute the trip's one real deadline.
Trigger narrowed to 關門**前**.

### A6 — Palette gains on-dark and chip-ground tokens (§3.2)

The implementation needed colours the approved palette did not name: secondary text on
the dark hero, the Fuji mark on dark, grounds for the 湯/買 chips, and the pale washes
behind panels and booked rows. These were raw hex/rgba literals, violating §4. They are
now tokens (`--kasumi`, `--beni-lite`, `--yu`, `--murasaki`, `--wash`, `--wash-firm`,
`--beni-wash`) with measured contrast, and §3.2 gains a dark-ground contrast table.

Two values were adjusted to pass 4.5:1 against the *lightest* hero gradient stop rather
than only against `--bero-deep`, where a first pass had measured 4.38:1 and 3.36:1:
`--kasumi` `#BFD6E8` → `#C8DEEF`, `--beni-lite` `#F0A79E` → `#FAD2CD`.

§4's 44px rule also gains the WCAG 2.5.8 exemption for in-sentence links, and §5's
emoji rule now covers link-label markers (📍/🌐 → 地/網) as well as stop titles.

## 7. Out of scope

- Root `index.html` and `map.html`, and all other trip folders.
- Renaming the `2025-10-04-10-12-tokyo/` folder (would break the deployed URL) or
  its entry in the root index.
- Adding new itinerary content, photos, or maps.
- Dark mode (§6.3).

## 8. Verification

The original files are kept at their base commit, so every check below compares the
new file against `git show <base>:<path>`.

1. **Content parity (automated).** A throwaway script under the session scratchpad
   compares old vs new per file. **All quantity comparisons are multisets** (value +
   occurrence count, via `sort | uniq -c`), not sets, so a dropped duplicate is
   caught:
   - `href` values: `new ⊇ old` as a *set*, with exactly two accepted differences:
     `new \ old` may contain only the §5 additions (`../index.html`, sibling-page
     links) plus the corrected URL from §5.1, and `old \ new` may contain only the
     malformed URL from §5.1. Any other missing or new value fails. Set semantics
     here because re-housing links legitimately changes how often a target repeats.
   - `<img src>` values: `new == old` as a multiset. No additions permitted.
   - Times `\d{1,2}:\d{2}`: `new == old` as a multiset.
   - Amounts `¥[\d,]+` / `[\d,]+ *日圓` / `[\d,]+ *元`: `new == old` as a multiset.
   - Reservation code `20342479764521208`: present.
   - **Long prose paragraphs**: strip tags, collapse whitespace, and extract every
     text run ≥ 60 characters from `old` (this captures all 24 景點/歷史背景
     paragraphs). Every one must appear verbatim as a substring of the normalised
     new text. This is the check that proves no prose was silently trimmed.
   - **Japanese proper nouns**: extract every katakana/hiragana run
     (`[ぁ-んァ-ヶー]{2,}`) as a multiset; require `new ⊇ old`. Catches dropped names
     like ホテルプリヴェ静岡 or バリ勝男クン.
   - **Opening hours / ranges**: extract `\d{1,2}:\d{2}\s*[~～-]\s*\d{1,2}:\d{2}`
     as a multiset; require `new == old`.
   - **Tables**: `index.html` still contains exactly 4 `<table>`; the total `<tr>`
     count matches old; and each 已預約 / 備選 marker still appears the same number
     of times.
   - **Stop count**: the original's 93 `.timeline-item` elements split into 9 overview
     rows and 84 day stops. Assert `index.html` has exactly 84 `.stop__body` and 9
     `.outline li`, totalling 93 — so no stop was dropped or duplicated while
     re-housing them.
   - **Identifiers** (flight and service numbers): multiset of `[A-Z]{2,}\d{2,}`
     (JX802, JX803, JT16, …) and of `\d+[号號]` (N'EX 34号/38号, 42號, 818號, 11號,
     5號, …) must be `new == old`.
   - **Proper nouns**: multisets of every `【…】` and every `「…」` span must satisfy
     `new ⊇ old`. These brackets carry most attraction and line names
     (【駿府城公園】,「富士山・靜岡地區周遊券 Mini」, …).
   - **Hotel and station names**: assert all **4** distinct lodgings are present —
     鎌倉大船JR東日本METS飯店 (nights 1 & 7) · ホテルプリヴェ静岡 (2, 3, 4, 6) ·
     修善寺温泉 桂川 (5) · 橫濱站前里士滿酒店 (8) — covering all 8 nights, together
     with the **7** distinct nightly price strings (`20,952日圓` recurs for nights 1
     and 7). Station names are covered by the katakana, bracket and ≥60-char prose
     checks above.
2. **Content parity (manual).** Automated checks cannot prove the *right* text sits
   under the right day. Read old and new side by side, one pass per day, ticking a
   9-row checklist (一…九) plus 行程總覽 — confirming for each: the stop order is
   unchanged, each stop's time/title/transport/note still belong together, and the
   day's 住宿資訊 and 相關連結 carry that day's own values. Same pass for the two
   sub-pages (7 shop cards; 富邦 two 回饋 tiers + 玉山 two cards + 使用建議).
3. **No inline styles** — `grep -c 'style="'` returns 0 on all three files.
3a. **Chip classification** — a script re-derives the chip for all 84 stops from the
   §3.5 ordered rule (title only) and compares it to the markup. Required result:
   **0 mismatches**, i.e. the rule fully explains the page with no editorial
   exceptions.
3b. **Token discipline** — no six-digit hex literal appears anywhere in a `<style>`
   block outside the `:root` token declarations and the exempt hero artwork
   (`.ridge__*` fills, the `linear-gradient` stops); and `color:var(--nami)` appears
   zero times, in any file.
3c. **Touch targets** — measured in a real 320px viewport: every `a`/`button`/`summary`
   whose computed `display` is not `inline` has a rendered height ≥44px. Inline
   in-sentence links are exempt per §4.
4. **Anchors** — `#summary` and `#day1`…`#day9` all present and reachable from the
   nav.
5. **Rendering** — open each page in a browser at 320 / 390 / 768 / 1280px; confirm
   `document.documentElement.scrollWidth === clientWidth` at each width (per the §3.4
   definition, with the day rail as the only internally scrolling element), day
   expand/collapse works, sticky rail tracks the active day and scrolls the active
   item into view, back-to-top works, and the auto-expand fallback opens 行程總覽.
6. **Keyboard/a11y** — tab through each page: every link and every day summary shows
   a visible focus ring and days toggle with Enter/Space.
7. **Reduced motion** — with `prefers-reduced-motion: reduce`, no animation runs.
