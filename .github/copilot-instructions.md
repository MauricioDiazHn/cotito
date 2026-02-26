<!-- Copilot / AI agent instructions for the COTITO repo -->
# Copilot instructions — COTITO

Purpose: Help AI coding agents make focused, low-risk changes to this repository (static marketing site + email template).

- Big picture:
  - This is a simple static site: the single-page UI lives in `index.html` (inline CSS + JS). The transactional email template is `email.html`.
  - No build system, no package.json, and no backend code in this repository. Deployments are done by serving static files and integrating with external services (n8n, Spline, Telegram/Discord links).

- Key files / directories:
  - `index.html` — main SPA-like static page. Contains: Tailwind CDN usage, custom `<style>` in `<head>`, inline `<script>` at the bottom implementing theme toggle, contact form, carousels and webhook calls.
  - `email.html` — HTML email template with placeholders `{{name}}`, `{{email}}`, `{{message}}` (used by n8n or similar templating flows).
  - `img/` — images and logos (`CotitoHn_LogoDark.png`, `CotitoHn_LogoWhite.png`, `CotitoHn_Icon.png`).
  - `videos/` — video assets referenced by the site.

- Discovered integrations and important notes:
  - Contact form posts directly to an n8n webhook URL in `index.html`:
    `https://n8n-dbgnqsta.us-east-1.clawcloudrun.com/webhook/contact-form` — do not change this URL without updating the n8n flow and verifying CORS/secret handling.
  - `email.html` uses `{{...}}` placeholders — these are expected by the external workflow (n8n) to populate email content.
  - A Spline scene is embedded via `<spline-viewer url="...">`. Changes to Spline usage require updating the external scene and validating the viewer URL/version.

- Code patterns and conventions to follow (only describe what exists):
  - Keep UI changes inside `index.html` unless adding new static assets. Styles are a mix of Tailwind utility classes and a repo-specific custom `<style>` block at the top of `index.html`.
  - JavaScript is inline and DOM-focused. Functions like `goToSlide` and `goToVideoSlide` are attached to `window` for global access; keep that pattern when editing or adding simple behaviors to avoid breaking inline event handlers.
  - Dark mode is controlled via `document.body.dataset.theme = 'dark'` and a toggle `#theme-toggle` checkbox — preserve this mechanism when changing logos or theme colors.

- Local dev / preview tips (no build step):
  - Preview by opening `index.html` in a browser or run a local static server from repository root. Example (PowerShell):
    ```powershell
    # Using Python (if installed)
    python -m http.server 8000
    ```
  - Alternatively, use the VS Code Live Server extension to preview changes immediately.

- How to edit safely and validate changes:
  - For JS changes that call external services (webhook), log requests and responses (as the repo already does) and avoid hardcoding secrets in this repo.
  - When modifying the contact form, validate that the `Content-Type: application/json` POST and payload keys match expectations in the external webhook's flow.
  - If changing assets (logo filenames or paths), update both `index.html` and `email.html` references.

- Examples & quick references (from the codebase):
  - Contact POST (index.html):
    ```js
    const webhookUrl = 'https://n8n-dbgnqsta.us-east-1.clawcloudrun.com/webhook/contact-form';
    fetch(webhookUrl, { method: 'POST', headers: { 'Content-Type': 'application/json' }, body: JSON.stringify(data) });
    ```
  - Email template placeholders (email.html): `{{name}}`, `{{email}}`, `{{message}}` — do not rename these without updating the external template renderer.

- When to create new files or refactor:
  - Small UI or behavior fixes can be applied inline in `index.html`.
  - If adding significant JS (~>200 lines), prefer to add a new file in a top-level `js/` directory and include it via a `<script src="js/...">` tag, keeping changes modular.

- What to avoid:
  - Do not introduce server-side or secret-bearing code into this repo. Secrets belong in the external workflow (n8n) or deployment platform.
  - Do not change the webhook URL or email placeholders without coordinating with the n8n flow owner (they are live integrations).

If anything above is unclear or you want more specifics (CI, deployment steps, or n8n flow IDs), tell me which area to expand and I will iterate.
