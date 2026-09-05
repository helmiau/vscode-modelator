# Product Requirements Document (PRD)

## 9Router VSCode Modelator

### Vision
Browser-based tool that generates `chatLanguageModels.json` for VS Code Copilot Chat from any OpenAI-compatible `/v1/models` endpoint.

### Target Users
- Developers using VS Code Copilot Chat
- Users of custom AI providers (9Router, OmniRoute, CLIProxyAPI, SwitchIt)
- Anyone needing to configure custom model providers in VS Code

### Core Features

#### 1. Model Fetching
- Fetch from OpenAI-compatible `/v1/models` endpoints
- Batch fetch & merge multiple endpoints
- Individual endpoint fetch
- Support for URL, Paste JSON, Upload source types

#### 2. Conversion & Editing
- Convert raw model lists to `chatLanguageModels.json` format
- Tree view with multi-select, batch delete, batch edit
- Code editor view (Ace.js) with JSON formatting
- Code ↔ Tree synchronization

#### 3. Installation
- One-click download of `chatLanguageModels.json`
- Install directly to VS Code user directory
- Support for custom install paths
- OS detection (Windows/macOS/Linux)

#### 4. User Interface
- Dark/Light theme
- Responsive design (mobile-first)
- Collapsible panels
- Activity log
- i18n support (English, Indonesian, French, Japanese, Vietnamese, Chinese)

### Technical Requirements

#### Frontend
- Vanilla HTML/CSS/JS (zero runtime dependencies)
- Ace.js for code editor
- Google Material Symbols CDN
- No build step required

#### Backend (Optional)
- Python localhost relay for CORS/mixed-content issues
- WebSocket relay mode
- HTTP CORS proxy mode
- App mode (same-origin proxy)

#### Browser Compatibility
- Chrome 111+ (color-mix support)
- Firefox (with CSS workarounds)
- Edge, Safari

### Success Metrics
- Users can generate valid `chatLanguageModels.json` in <5 minutes
- Zero server setup for basic use
- Works on HTTPS via Cloudflare tunnel
- Handles 1000+ models without performance issues

### Future Enhancements
- Provider templates (pre-configured endpoints)
- Model filtering by capabilities
- Export to multiple formats
- VS Code extension integration
- Cloud sync of configurations

---

## Appendix A: UI/UX Audit — 2026-08-21

> **Sumber:** `ui-ux-pro-max` skill — `--design-system "developer tool dark minimal code editor"` + domain search `ux` / `style` / `color` + manual code review `index.html` / `assets/style.css` / `assets/app.js`.
> **Stack:** Vanilla HTML/CSS/JS + Ace.js | **Style:** Dark Mode OLED / Modern Minimal | **Pattern:** Single Column + Drawer Nav
> **Status:** Open — belum diimplementasi (Top 10 fix diprioritaskan di bawah).

### Ringkasan Eksekutif

Design system check merekomendasikan **Dark Mode OLED** (`#0F172A` + accent `#22C55E`, font `JetBrains Mono / IBM Plex Sans`) — implementasi saat ini sudah selaras (dark default `#1b1b1b`, accent `#ebebeb`/`#9eb7ff`, font `Inter` + `JetBrains Mono`). Tidak perlu ganti palet, hanya perbaikan token & kontras. Audit menemukan **7 issue CRITICAL/HIGH** di Accessibility dan Touch Target, serta 3 issue HIGH di Layout/Performance yang perlu diperbaiki sebelum rilis.

### Design System Rekomendasi (dari `--design-system`)

| Dimensi | Rekomendasi | Catatan untuk proyek ini |
|---------|-------------|--------------------------|
| **Pattern** | Minimal Single Column — Single CTA focus, large typography, whitespace | Sudah dipakai (`.container max-width:560px` + drawer). Pertahankan. |
| **Style** | Dark Mode (OLED) — deep black, midnight blue, minimal glow | Sudah dark default. Jangan jadikan light sebagai default. |
| **Colors** | Primary `#1E293B`, Accent `#22C55E`, Background `#0F172A`, Border `#475569` | Token saat ini `#1b1b1b`/`#262626`/`#9eb7ff` masih dalam spektrum yang sama — cukup selaraskan token, tidak perlu rebrand. |
| **Typography** | JetBrains Mono / IBM Plex Sans (code/developer mood) | Saat ini `Inter` + `JetBrains Mono` — sudah tepat, pertahankan. |
| **Effects** | Minimal glow `text-shadow: 0 0 10px`, dark-to-light transitions | Sudah ada `cubic-bezier(0.05,0.7,0.1,1.0)` — pertahankan. |
| **Avoid** | Light mode default + Slow performance | Jangan invert default ke light; lazy-load Ace.js. |

Domain search tambahan:
- `ux: accessibility animation loading` → skeleton/spinner untuk async, jangan biarkan UI freeze; animasi infinite hanya untuk loader.
- `style: dark minimal developer` → OLED `#000000`/`#121212` + neon accent, `color-scheme: dark`, kontras 7:1+.
- `color: developer tool` → Code dark `#1E293B` + run green `#22C55E` — validasi kontras AA/AAA.

### Temuan Detail

#### 1. Accessibility — CRITICAL

| Issue | Lokasi | Severity | Fix |
|-------|--------|----------|-----|
| **Topbar bukan button** — `div.topbar-left onclick="toggleSidebar()"` tidak focusable, tanpa `role="button"`, `tabindex`, `aria-label`. Keyboard & screen reader tidak bisa buka nav. | `index.html:14` | CRITICAL | Ganti jadi `<button>` atau tambah `role="button" tabindex="0" aria-label="Toggle navigation" aria-expanded` + handler `keydown Enter/Space` |
| **Heading hierarchy skip** — Tidak ada `<h1>`. Judul pakai `<span class="topbar-title">`, subtitle `<p>`. Panel `info-panel` langsung `h2`. | `index.html:17-18` | HIGH | Jadikan topbar title `h1` (visually same), panel heading `h2→h3` berurutan |
| **Icon-only buttons tanpa aria-label** — `.panel-btn`, `.copy-btn`, `.paste-btn`, `.toggle-vis-btn` hanya `title`, tanpa `aria-label`. | `index.html` + `app.js:render` | HIGH | Tambah `aria-label` di semua icon-only button. `title` tidak dibaca screen reader |
| **No skip link** | global | MEDIUM | Tambah `<a href="#contentArea" class="skip-link">Skip to content</a>` |
| **No aria-live untuk status/log** — `div#status` dan `logEntries` update dinamis tanpa `aria-live="polite"` / `role="status"` | `index.html:88, 210` | MEDIUM | `id="status" role="status" aria-live="polite"`, `id="logEntries" aria-live="polite"` |
| **Kontras gagal** — `--color-text-disabled: #525252` di `#1b1b1b` ≈ 2.1:1 (butuh 4.5:1). Dipakai di `.topbar-subtitle`, `.sidebar-footer`, `.hint`. Light mode `#a3a3a3` di `#ffffff` ≈ 2.4:1 | `style.css: :root` | HIGH | Naikkan ke `#767676` (dark) dan `#6b6b6b` (light) atau jangan pakai untuk body text |
| **Focus ring hilang di custom combobox** — `.ep-source-combo-row input[readonly]` tidak ada focus indicator saat keyboard nav | `style.css: ~650` | MEDIUM | Pastikan `:focus-visible` tetap trigger di wrapper |

**Positif:** Modal sudah `role="dialog" aria-modal="true"`, flag `alt` ada, `:focus-visible` 2px blue sudah didefinisikan, `prefers-reduced-motion` sudah handle.

#### 2. Touch & Interaction — CRITICAL

| Issue | Detail |
|-------|--------|
| **Touch target <44px** — `.sidebar-item 40px`, `.panel-btn 28px`, `.topbar-btn 38px`, `.tree-check 14px`, `.modal-close-btn 28px` — gagal Apple HIG 44pt / Material 48dp | Perbesar ke `44px` atau tambah `padding` + `hitSlop` (CSS `min-height:44px; min-width:44px`) |
| **Hover-only sidebar** — `#sidebarHoverZone` (12px edge) buka drawer di desktop `hover:hover`. Tidak ada affordance, rawan accidental open | Hapus atau jadikan persistent sidebar di `≥1024px` |
| **No loading feedback di Generate** — `pipeGenerate()` ada `data-state="loading"` tapi tidak disable form, user bisa double-click | Disable semua endpoint input saat `loading`, tampilkan spinner di button (sudah ada CSS `.btn-loading`) |
| **Destructive tanpa konfirmasi** — `deleteSelectedModels()` langsung hapus tanpa dialog `confirm` | Tambah `confirmation-dialog` + toast `Undo` (sesuai `ux: confirmation-dialogs`, `undo-support`) |
| **No press feedback** — `.endpoint-row` tidak ada `active` state | Tambah `transform: scale(0.99)` atau `background` change 80–150ms |

#### 3. Performance — HIGH

| Issue | Fix |
|-------|-----|
| **Ace.js blocking** — `<script src="cdnjs...ace.min.js">` di `<head>` tanpa `async/defer`, block render | Pindah ke `defer` + `initAce()` lazy saat panel Editor pertama dibuka |
| **Google Fonts FOIT** — `fonts.googleapis.com` tanpa `font-display:swap`, tanpa `preload` | Tambah `&display=swap` di URL, `rel="preload" as="style"` untuk Inter |
| **No CLS reserve untuk Ace** — `#aceEditor` `flex:1` tapi hidden awal, saat switch ke Code view layout shift | Set `min-height: 300px` atau `aspect-ratio` placeholder |
| **No virtualization** — `renderTreeView()` render semua model via `innerHTML` string concat. 1000+ model = jank, `main-thread-budget` >16ms | Virtualize list (render 50 visible + `IntersectionObserver`) atau pagination |
| **No debounce** — `filterLanguages()` tiap keystroke, `saveEndpoints()` tiap input | Debounce 150ms |

#### 4. Style Selection — HIGH

- Konsisten: `Material Symbols Outlined` + `Inter` + `JetBrains Mono` — bagus, tidak pakai emoji.
- **Issue:** Drawer di semua viewport tidak cocok untuk desktop developer tool. Rekomendasi: `@media (min-width:1024px) .sidebar { position:sticky; transform:none; width:240px }` + hide overlay.
- **Elevation inconsistent** — `box-shadow: 8px 0 24px` (sidebar) vs `0 24px 60px` (modal) vs `0 10px 30px` (token) — tidak pakai scale sistematis. Definisikan `--shadow-sm/md/lg`.

#### 5. Layout & Responsive — HIGH

| Issue | Detail |
|-------|--------|
| **Base font 14px** — `body 0.875rem` < 16px, iOS auto-zoom saat focus input, gagal `readable-font-size` | Naikkan ke `16px` di mobile, `14px` hanya untuk caption |
| **Pipebar overlap** — `.pipebar` fixed bottom, tapi `.content-panel.active > .main-panel` tidak ada `padding-bottom` untuk pipebar height (~56px). Konten terakhir tertutup | Tambah `padding-bottom: calc(56px + env(safe-area-inset-bottom))` di `.main-panel` & `.side-panel` |
| **No safe-area** — Tidak ada `env(safe-area-inset-*)` untuk notch/Dynamic Island | Tambah `padding: env(safe-area-inset-top) env(safe-area-inset-right) env(safe-area-inset-bottom) env(safe-area-inset-left)` di topbar & pipebar |
| **Container 560px terlalu sempit di desktop** — `max-width:560px` di 1440px menyisakan whitespace besar | Naikkan ke `720px` atau `max-w-3xl`, atau split layout 2 kolom di desktop |
| **Horizontal scroll risk** — `.curl-box code { white-space:pre }` + `overflow-x:auto` di dalam flex tanpa `min-width:0` bisa overflow viewport 320px | Pastikan parent `min-width:0` dan `overflow-wrap: anywhere` |
| **Breakpoint tidak sistematis** — Hanya `768px` dan `480px`, tidak ada `1024/1280` | Tambah `1024px` untuk sidebar persistent |

#### 6. Typography & Color — MEDIUM

- `line-height 1.5` & `tracking` token bagus.
- **Issue:** `color-semantic` — Raw hex masih di beberapa tempat (`rgba(255,255,255,0.1)` di border) bukan token. Ganti ke `var(--color-border)`.
- **Dark mode desaturasi** — `--color-success #9fe59b` terlalu terang di dark, kontras 1.8:1 vs bg. Pakai desaturated `#7bc47a` untuk dark, `#007004` untuk light (sudah benar di light).

#### 7. Animation — MEDIUM

- Durasi 150–350ms + `cubic-bezier(0.05,0.7,0.1,1.0)` sudah sesuai `duration-timing` & `easing`.
- **Issue:** `transform-performance` — `@keyframes panelFadeIn` pakai `transform: translateY + scale` (bagus), tapi `slideUpIn` animasi `max-height` & `padding` — trigger layout reflow. Ganti ke `transform` saja.
- `prefers-reduced-motion` sudah benar (`animation-duration:0.01ms`).

#### 8. Forms & Feedback — MEDIUM

- Label visible ada, helper text ada (`ep-hint-box`).
- **Issue:** `error-placement` — Error hanya di `div#status` global, tidak di bawah field yang error. User tidak tahu endpoint mana gagal.
- **No inline validation** — Validasi hanya saat Generate, tidak `on blur`.
- **No required indicators** — Field `Endpoint URL` & `API Key` wajib tapi tidak ada `*` atau `aria-required`.
- **No empty state** — `endpointList` kosong awal tidak ada ilustrasi/CTA, hanya hint box.

#### 9. Navigation — HIGH

- **No deep linking** — `switchPanel()` tidak update `location.hash` atau `history.pushState`. Back button browser tidak kembali ke panel sebelumnya, tidak bisa share link ke Editor.
- **No active state persistence** — Refresh hilang panel position (kecuali endpoint di localStorage).
- **Bottom nav limit** tidak relevan (web), tapi drawer 5 item sudah ideal (≤5).

#### 10. Charts & Data — LOW

- Tidak ada chart di app ini — tidak relevan. Jika nanti ada analytics dashboard, ikuti `chart` domain: legend, tooltip, accessible colors, `prefers-reduced-motion`.

### Top 10 Fix Prioritas

| # | Fix | Kategori | Effort |
|---|-----|----------|--------|
| 1 | Topbar button a11y + touch target 44px | Accessibility / Touch | S |
| 2 | Pipebar safe-area + padding-bottom (konten tertutup di mobile) | Layout | S |
| 3 | Kontras disabled text `#525252` → `#767676` | Accessibility | XS |
| 4 | Base font 16px mobile (cegah iOS zoom) | Layout | XS |
| 5 | Persistent sidebar ≥1024px (jangan drawer di desktop) | Style/Layout | M |
| 6 | Deep linking hash — `switchPanel()` → `history.pushState` | Navigation | S |
| 7 | Error near field + `aria-live` status | Forms/Accessibility | M |
| 8 | Confirm delete + Undo toast | Interaction | S |
| 9 | Ace.js defer + lazy init | Performance | S |
| 10 | Virtualize tree view untuk 1000+ model | Performance | L |

### Pre-Delivery Checklist (dari skill)

- [ ] No emojis as icons (use SVG: Heroicons/Lucide) — **PASS**
- [ ] cursor-pointer on all clickable elements — cek `.topbar-left`, `.tree-toggle`
- [ ] Hover states 150–300ms — **PASS**
- [ ] Light mode text contrast 4.5:1 — **FAIL** (disabled text)
- [ ] Focus states visible — **PASS** (tapi combobox perlu fix)
- [ ] prefers-reduced-motion respected — **PASS**
- [ ] Responsive: 375px, 768px, 1024px, 1440px — **FAIL** (1024 belum ada)
- [ ] Touch targets ≥44pt — **FAIL**
- [ ] Safe areas respected — **FAIL**
- [ ] Dark mode contrast independen — **FAIL** (success color)