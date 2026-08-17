# Agents Guide

## Project: 9Router VSCode Modelator

### Overview
Browser-based tool for generating `chatLanguageModels.json` from OpenAI-compatible endpoints.

### Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    Browser (HTTPS)                           │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐         │
│  │   index.html│  │  style.css  │  │   app.js    │         │
│  │   (markup)  │  │   (styles)  │  │   (logic)   │         │
│  └─────────────┘  └─────────────┘  └─────────────┘         │
└──────────────────────────┬──────────────────────────────────┘
                           │
┌──────────────────────────▼──────────────────────────────────┐
│              Python Relay (Optional)                         │
│  ┌─────────────────────────────────────────────────────────┐│
│  │ localhost_relay.py --app --api-url http://localhost:20128││
│  │ Serves web app + proxies API calls (same-origin)       ││
│  └─────────────────────────────────────────────────────────┘│
└─────────────────────────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────┐
│              OpenAI-Compatible API (HTTP)                  │
│  http://localhost:20128/v1/models                            │
└─────────────────────────────────────────────────────────────┘
```

### Key Files

| File | Purpose |
|------|---------|
| `index.html` | Main app markup, 5 panels (Form, Editor, Scripts, Log, About) |
| `assets/style.css` | All styling (~2000 lines), dark/light theme, responsive |
| `assets/app.js` | All logic, panel switching, API calls, tree operations |
| `scripts/localhost_relay.py` | Optional relay server for CORS/mixed-content |
| `lang/*.json` | i18n translations (6 languages) |
| `lang/i18n.js` | Fallback translation strings |

### Development Workflow

1. **Edit files** — Changes to `index.html`, `assets/*.css`, `assets/*.js` are live
2. **Reload browser** — Always reload with cache-buster `?v=timestamp`
3. **Test relay** — Run `python scripts/localhost_relay.py --app --api-url http://localhost:20128`
4. **Verify** — Check browser console for errors

### Common Tasks

#### Add New i18n Key
1. Add to `lang/en_US.json` (and `id_ID.json`)
2. Add fallback to `lang/i18n.js`
3. Use via `t('key.name')` in JS or `data-i18n="key.name"` in HTML

#### Add New CSS Variable
1. Define in `:root` (dark theme) and `body.light` (light theme)
2. Use via `var(--variable-name)`

#### Add New Button
1. Add to `index.html` with `data-i18n-title` and `data-i18n`
2. Style via `.pipe-btn` classes
3. Add click handler in `app.js`

### Browser Testing

Always test at these breakpoints:
- 320px (iPhone SE)
- 375px (iPhone 14)
- 414px (iPhone Plus)
- 480px (small Android)
- 560px (compact)
- 768px (iPad portrait)
- 1024px (iPad landscape)
- 1280px (desktop)

### Error Handling

The relay displays helpful messages for common errors:
- **Port in use** → "unduh dan jalankan helper script"
- **Missing websockets** → "unduh dan jalankan helper script"
- **Invalid API URL** → Shows error in UI
- **Mixed content (HTTPS→HTTP localhost)** → Shows `corsHint` with "Unduh Relay" button to download helper script

### Deployment

1. **GitHub Pages** — Push to `gh-pages` branch
2. **Cloudflare Tunnel** — `cloudflared tunnel --url http://localhost:9877`
3. **Local** — `python scripts/localhost_relay.py --app --api-url http://localhost:20128`

### Future Agent Tasks

- Add new UI components
- Implement new API endpoints
- Add language translations
- Optimize performance
- Fix browser compatibility issues
- Update documentation