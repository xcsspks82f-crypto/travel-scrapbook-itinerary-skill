---
name: travel-scrapbook-itinerary
description: "Use when the user provides a travel itinerary, OCR itinerary text, table itinerary, screenshot-derived text, or rough trip idea with origin/destination/dates/must-go places and wants a Chinese 9:16 vertical travel scrapbook itinerary prompt pack or generated scrapbook images. Specializes in route-first travel-flow visualization: parsing days, evaluating feasibility, supplementing weather/temperature, transportation, lodging areas, attractions, food, timing, tickets, reminders, density-based page splitting, optional user-provided mascot sticker placement, replaceable photo slots, and AI image prompts/images for practical phone-readable travel handbook pages rather than generic travel guides or decorative collages."
---

# Travel Scrapbook Itinerary

## Mission

Turn a user's itinerary or rough trip idea into a route-first 9:16 Chinese travel scrapbook prompt pack or generated image set. Visual beauty is secondary; the first priority is that travelers can understand the route, timing, transfers, hotel changes, ticket/opening constraints, weather risks, and reminders while on the trip.

If an image-generation tool/API is available and the user asks to generate images, generate one 9:16 image per planned page by default. If no image tool is available, output copy-ready prompts plus replaceable image slot manifests.

## Public/Private Asset Boundary

This Skill is designed to be open-source safe. The public Skill must contain only reusable logic, prompt templates, configuration schema, and text style rules.

Do not assume or ship any proprietary cartoon IP. Use user-uploaded characters, a local private character profile, or generic public placeholder companions. Load `references/open-source-ip-policy.md` when preparing the Skill for public release, packaging, licensing, or character asset handling.

## Load These References

- Load `references/prompt-template.md` when writing final image prompts.
- Load `references/visual-style-guide.md` before writing page plans or prompts.
- Load `references/itinerary-structure-rules.md` when parsing screenshots/OCR itinerary text, handling "先不要生图", or deciding whether a sparse day should stay separate.
- Load `references/rough-idea-trip-builder.md` when the user gives only origin, destination, dates, and a few must-go places.
- Load `references/live-data-automation.md` when the user asks for weather/temperature prediction, nearby snacks/restaurants, group-buying/social-platform recommendations, lodging candidates, transport schedules, or fully automated trip planning.
- Load `references/itinerary-feasibility-rules.md` when the user asks whether the route is reasonable, asks for itinerary optimization, or asks Codex to supplement scenic/food visuals.
- Load `references/character-system.md` when the user provides character references, mentions mascots, or asks to include travelers as stickers.
- Load `references/replaceable-image-slots.md` when generating final image prompts, recording generated image results, or handling requests to replace photos inside an existing scrapbook page.
- Load `references/end-to-end-generation-workflow.md` when checking whether the Skill can turn a travel plan into final scrapbook images or when explaining the full pipeline.
- Load `references/default-config.yaml` when the user asks about settings, UI behavior, batch image generation, backend configuration, or public/private asset setup.
- Load `references/open-source-ip-policy.md` when the user asks about open-sourcing, licensing, sharing, character ownership, public repos, or preventing private IP leakage.

## Workflow

### 1. Normalize the Input

Accept plain text, OCR text from screenshots, tables, rough trip ideas, and casual descriptions. Preserve user-provided facts exactly: dates, origin/destination, must-go places, flight numbers, train numbers, times, hotel names, addresses, ticket prices, and notes must not be rewritten or "improved".

If the user provides only a rough idea, first build a practical itinerary with `references/rough-idea-trip-builder.md` and `references/live-data-automation.md`: identify assumptions, run live lookup for weather/temperature, transport, lodging, food/snacks, opening hours, and tickets when possible, then recommend route, transport, lodging areas, food, and page split before generating image prompts.

If a field is missing, write `待补充` or `未提供`. If adding nearby places, food, photos, or supply points, mark them as `建议补充`; never present them as confirmed itinerary facts.

### 2. Structure the Trip

Output a table under `【1. 行程结构化拆解】` with these columns:

`Day / 城市 / 交通 / 景点 / 酒店 / 美食 / 关键提醒`

Inside the cells, capture date, departure and arrival, flight/train number, departure and arrival time, hotel address, tickets, opening times, time cutoffs, user notes, day type, and risk warnings when present. Keep the table compact enough to read.

Classify each day as one or more of:

- `赶路日`: cross-city movement, flight/train, car pickup/dropoff, hotel switch, or tight transfer.
- `轻松游玩日`: same-city, low fixed-time pressure, mostly free activity.
- `重点提醒日`: ticket/opening-hour dependency, hard departure deadline, complex transfer, heavy route density, weather risk, or likely fatigue.

### 3. Decide Page Count by Information Density

Do not make one page per day mechanically. Build page groups by day density and route continuity.

If an earlier draft or user wording says `每天一页`, still run the density check unless the user explicitly insists that every day must be separate. The product goal is a phone-readable travel flow, not a rigid calendar.

Use these signals as single-page reasons:

- Cross-city transport.
- Explicit flight number or train number.
- Hotel switch.
- Two or more core attractions.
- Ticket, opening time, reservation, or hard cutoff reminder.
- Opening day or return day.

Merge adjacent days only when all are mostly true:

- Same city or same travel theme.
- Light same-city travel.
- Few fixed time points.
- No complex transport or hotel switch between the merged days, except a simple end-of-day return segment that can be treated as a reminder.
- The merged page still remains readable on a phone.

When a day is too packed, explicitly say `这天偏赶` and name the bottleneck: transfer time, opening hours, distance, hotel switch, weather, or too many attractions.

### 4. Plan Each Page as a Travel Flow

For every page under `【3. 每页内容规划】`, include:

- 页面标题: `Day X` or `Day X-Y` plus city/theme.
- 路线顺序: a clear ordered route using arrows.
- 必放信息: transportation, hotel, attractions, food, tickets, opening times, time reminders, weather, and warnings.
- 可补充视觉元素: landmark photos, food photos, train/plane/car icons, map fragments, ticket stickers, weather or packing hints.
- 可替换图片槽位: stable slot IDs for scenic, food, hotel, transport, and memory photo stickers.
- 角色动作建议: small traveler/mascot stickers matched to the page situation.
- 版式建议: large Day badge, route map logic, card placement, reminder bubble placement, and density control.

Make route logic visible: each page should have a gray dashed path or numbered route spine connecting transportation, hotel, attractions, food, and reminders in the correct order.

### 5. Use Characters as Supporting Stickers

Character priority is:

1. User-uploaded character reference images in the current request.
2. Local private profile at `private/character-profile.local.yaml`, if it exists.
3. Generic public placeholder companions from `references/character-system.md`.

Keep characters small. They should appear as corner stickers, interaction stickers, or reminder stickers, never as the page subject. If no private or user-provided characters are available, do not use proprietary names or distinctive private IP traits.

Choose actions by page type:

- 出发页: dragging luggage, waving, boarding plane/train.
- 山水页: taking photos, looking at scenery, surprised reaction.
- 赶路页: carrying luggage, catching train, checking time.
- 海边页: eating seafood, watching the sea, taking photos.
- 返程页: waving, packing luggage, boarding.

### 6. Generate Copyable Image Prompts

Under `【4. 每页生图提示词】`, write one complete prompt per page. Each prompt must be independently copyable; do not write "同上". Use `references/visual-style-guide.md` for visual grammar, then use `references/prompt-template.md` and fill every placeholder with the page's actual route and facts.

Also use `references/replaceable-image-slots.md` so the prompt describes distinct replaceable photo sticker slots rather than a flattened collage.

Every prompt must include:

- `vertical 9:16`.
- Chinese travel scrapbook itinerary page.
- Beige grid-paper background.
- Sticker-like cutout photos with white borders.
- Gray dashed route lines.
- Handwritten Chinese labels.
- Large `Day X` date bubble.
- Attraction, transport, food, hotel, ticket, weather, and reminder cards when relevant.
- Black handwritten Chinese text.
- Cute but practical layout.
- Clear route, high information density, and no clutter.

The prompt must read like a phone-readable travel process board, not a poster. Route order, time constraints, transport cards, hotel cards, weather cards, and reminder bubbles must be explicitly described before decorative elements.

### 7. Output in the Required Contract

For full prompt-generation requests, always use this section order:

`【1. 行程结构化拆解】`

`【2. 手账页数建议】`

State how many 9:16 images to create and why the split is route-readable. Include titles such as `Page 1: Day1｜抵达日`.

`【3. 每页内容规划】`

`【4. 每页生图提示词】`

`【5. 批量生成说明】`

For batch generation, default to 9:16, one image per page, all pages generated from their own prompt. Allow regenerating a single page by page number.

For full prompt-generation requests, include replaceable image slot manifests inside each page plan or under `【5. 批量生成说明】`; this is required for later photo replacement.

If the user selected `只拆解行程` or says `先不要生图 / 先拆解内容`, output only `【1. 行程结构化拆解】`, `【2. 手账页数建议】`, and `【3. 每页内容规划】`; add a compact `待补信息` note when fields are missing, and do not write final image prompts until the user asks for them.

If the user says an existing generated page is good and only wants to replace pictures, keep the page split, route, text cards, characters, and layout stable; ask for or infer the target slot ID, then generate a replacement instruction for that slot only.

## Hard Rules

- Optimize for travel-day usability before aesthetics.
- Make route, time, weather, and reminders visible.
- Highlight hard time points, departures, arrivals, opening hours, ticket prices, hotel switches, and weather risks.
- Never change flights, trains, hotels, addresses, ticket prices, or user notes.
- For weather, transport schedules, hotel availability/prices, restaurant status, ticket policy, and opening hours, run live lookup by default. Use fallback sources automatically if the first source fails; only mark `待实时查询` after lookup attempts fail.
- Do not invent non-existent places.
- Mark additions as `建议补充`.
- Merge low-density days when it improves readability.
- Keep all generated images vertical 9:16 by default.
- Do not use proprietary character names, likenesses, or private reference assets unless the user supplied them in the current request or a local private profile is present.
- Keep output direct, copyable, and free of generic travel-guide filler.
