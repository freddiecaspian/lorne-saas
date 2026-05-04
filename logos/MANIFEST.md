# Logo Manifest

Fetched 2026-05-04 via Playwright MCP. Sources are the vendor's own site or official CDN; nothing from Clearbit, Wikipedia, Google Image search, or favicons-as-logos (except where noted).

## Booking platforms

| File | Format | Source URL |
|---|---|---|
| `opentable.svg` | SVG (3.1 KB) | `https://www.opentable.com/static/cfe/15/images/opentable-logo-ZGYRVF2L.svg` (homepage header) |
| `sevenrooms.svg` | SVG (5.7 KB) | `https://cdn.builder.io/api/v1/image/assets%2Facabda4a1104467f93cbad15a924c9a3%2F74aaeac2a0a848ce8185119c06f1a23d` (sevenrooms.com header, served via Builder.io CDN) |
| `resy.svg` | SVG (1.5 KB) | Inline SVG extracted from resy.com header (`.ResyIcon--logo svg`), Resy red `#ff462d` wordmark |
| `tock.svg` | SVG (1.9 KB) | Inline SVG extracted from exploretock.com header (`a[aria-label="Tock home page"] svg`) |

## Generic AI

| File | Format | Source URL |
|---|---|---|
| `chatgpt.svg` | SVG (4.2 KB) | `https://chatgpt.com/cdn/assets/favicon-l4nq08hd.svg` - the ChatGPT blossom mark, official 180x180 SVG favicon (auto-themes to white in dark mode via inline `@media`) |
| `openai.svg` | SVG (1.2 KB) | OpenAI wordmark extracted from openai.com/brand (header SVG, viewBox 0 0 288 78) |
| `claude.svg` | SVG (2.5 KB) | `https://assets-proxy.anthropic.com/claude-ai/v2/assets/v1/cd02a42d9-Vq_H3mgS.svg` - the Claude product icon SVG from claude.ai |
| `anthropic.png` | PNG 256x256 | `https://cdn.prod.website-files.com/67ce28cfec624e2b733f8a52/67d31dd7aa394792257596c5_webclip.png` - Anthropic Apple webclip icon. Note: the wordmark on anthropic.com is rendered as a Lottie animation, no flat SVG asset is exposed; the webclip is the closest official mark |

## Voice AI

| File | Format | Source URL |
|---|---|---|
| `aedan-rose.png` | PNG 1024x1024 | `https://aedanrose.ai/assets/logo.PNG` - Aedan Rose product mark from header |
| `hostie-ai.png` | PNG 1070x442 | `https://cdn.prod.website-files.com/6803352d10c80e32009ee356/680335ee22d820a342762241_logo.png` - Hostie wordmark, header asset |
| `loman-ai.svg` | SVG (3.2 KB) | `https://cdn.prod.website-files.com/687189243447875e00850639/687950b3358c5bd427af7a85_Group%203.svg` - Loman AI nav header |
| `slang-ai.svg` | SVG (6.1 KB) | `https://cdn.prod.website-files.com/61aa7a8f9272d51e4f71a7c9/67fd9d881cd2fc29c1798dc4_Slang.svg` - Slang AI hero/header logo |
| `soundhound.svg` | SVG (6.4 KB) | `https://www.soundhound.com/wp-content/uploads/2023/09/soundhound_ai_logo_BLACK3-01.svg` - SoundHound AI black wordmark |

## UK booking

| File | Format | Source URL |
|---|---|---|
| `quadranet.svg` | SVG (4 KB) | `https://quadranet.co.uk/wp-content/themes/quadranet/static/img/logo-white.svg` - extracted from header CSS `background-image`. Branded `#c30055` magenta |
| `bookatable.png` | PNG 180x180 | `https://www.thefork.com/statics/apple-touch-icon.png` - Bookatable was acquired by Michelin in 2016 and the brand has since been rolled into TheFork (bookatable.com now redirects to thefork.com). Used the TheFork apple-touch-icon as the closest current successor brand |
| `booking-ninja.png` | PNG 1484x858 | `https://bookingninja.io/logo/BN_logo_happy.png` - the canonical domain is `bookingninja.io` (not `bookingninja.com`, which doesn't resolve) |
| `vouch-concierge.png` | PNG 498x226 | `https://www.vouchconcierge.com/wp-content/uploads/2022/01/Vouch_Logo_Hor_Black_RGB-1.png` - horizontal black wordmark. Note: vouchconcierge.com now redirects to vouch-technologies.com but the legacy CDN path still serves the asset |

## CRM / Email / Productivity

| File | Format | Source URL |
|---|---|---|
| `hubspot.svg` | SVG (2.8 KB) | Decoded from base64 data URI in hubspot.com global nav (`.global-nav-logo -static`). Official orange `#ff4800` HubSpot wordmark |
| `superhuman.svg` | SVG (4.3 KB) | Inline SVG sprite `#superhuman-logo` extracted from superhuman.com `<symbol>` definition, wrapped in standalone SVG. Wordmark (full Superhuman lockup) |
| `gmail.png` | PNG 1024x1024 | `https://www.gstatic.com/images/branding/product/2x/gmail_2020q4_512dp.png` - Google's official Gmail product logo (current 2020 envelope mark) from gstatic CDN |
| `microsoft-word.svg` | SVG (3.9 KB) | `https://www.microsoft.com/content/dam/microsoft/bade/images/icons/en-us/m365-app-icons-fy26/Word-Icon-FY26.svg` - the FY26 Word app icon from Microsoft's M365 brand asset CDN |

## Notes / caveats

- **Anthropic** uses a Lottie-animated wordmark on anthropic.com - there's no flat SVG of the full wordmark exposed, only the icon-style webclip. If a wordmark is needed for slide composition, consider redrawing or using the Claude product icon (`claude.svg`) since "Anthropic" and "Claude" both apply to that vendor.
- **Bookatable** is functionally dead as a brand (Michelin sold to TheFork c. 2019). The icon used is TheFork's, which now owns the customer base. Flag in Slide 12 copy if needed.
- **Vouch Concierge** has migrated to `vouch-technologies.com` but the old asset path still works. The wordmark is on a transparent background.
- **Aedan Rose** logo file is 1.8 MB at 1024x1024 - downscale before embedding in the slide.
- **OpenAI brand page** (openai.com/brand) returns 403 to direct GET; logo had to be scraped from the rendered DOM.

## What was NOT done

- No favicons used as primary logos (16x16 too small) - the only "favicon"-flavoured asset used is `chatgpt.svg`, which is the official 180x180 SVG asset chatgpt.com itself uses for its product mark. Same for `gmail.png` (Google's branded 1024x1024 product icon, not the 16x16 favicon).
- No Wikipedia, no Clearbit, no Google Image search.
- No cropping, recolouring, or alteration.
