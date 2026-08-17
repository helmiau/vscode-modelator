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