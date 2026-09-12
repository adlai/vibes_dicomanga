# Dico.Manga 📚✨

A comprehensive manga dictionary web application with detailed manga profiles, release calendar, user collections, statistics, and social features. Built as a progressive web app with offline support and multi-language i18n.

**Live:** [dicomanga.com](https://dicomanga.com)

## What this is

**Dico.Manga** is a browsable encyclopedia of 170+ manga titles with detailed information cards, cover art galleries, user ratings, and personalized tracking. Users can build favorites, reading lists, custom collections, participate in a real-time chat, read and write comments—all data syncing to the cloud or stored locally. The app works offline via Service Worker and supports theme customization.

### Stack
- **Language(s):** HTML5, CSS3, JavaScript (vanilla)
- **Backend/Auth:** Supabase (PostgreSQL, Auth, Realtime)
- **Architecture:** Client-side PWA with Service Worker caching strategy
- **Notable libraries:** 
  - Supabase JS SDK (auth, sync, chat, comments)
  - No build tool — direct browser execution
  - Emoji & GIF pickers for rich comments
  - Multi-language support (FR, EN, JA)

## How it's organized

```
dicomanga/
├── index.html              Main library/browse page
├── manga.html              Individual manga detail page
├── calendrier.html         2026 release calendar
├── stats.html              User statistics & achievements
├── privacy.html            Privacy policy
├── manifest.json           PWA manifest (offline install)
├── sw.js                   Service Worker (network caching strategy)
├── robots.txt              SEO
├── sitemap.xml             SEO
├── CNAME                   Custom domain (dicomanga.com)
├── js/                     Application logic
│   ├── app.js              Main app (UI, search, filters, modals, shortcuts, achievements)
│   ├── data.js             Manga dataset (170+ titles with metadata)
│   ├── manga.js            Detail page controller & comments
│   ├── stats.js            User statistics & badges
│   ├── calendar.js         Calendar view logic
│   ├── calendar-2026.js    2026 release data
│   ├── auth.js             Authentication (Supabase login/signup)
│   ├── sync.js             Data sync (localStorage ↔ Supabase)
│   ├── chat.js             Real-time chat (public & private DMs)
│   ├── comments.js         Manga page comments (emoji/gif reactions)
│   ├── theme.js            Theme switching (light/dark + custom colors)
│   ├── guide.js            First-visit onboarding modal
│   ├── translations.js     French & English strings
│   ├── translations-ja.js  Japanese strings
│   ├── emoji-picker.js     Comment emoji picker
│   ├── gif-picker.js       Comment GIF picker
│   ├── manga-slugs.js      URL-friendly slug mapping
│   ├── achats.js           Volume purchase tracking
│   └── supabase-config.js  Supabase client init + auth listener
├── css/
│   └── style.css           All styling (responsive, PWA-friendly, 220+ KB)
├── images/                 Logo, favicon, avatars, mascot (Dicomi)
│   ├── logo.svg
│   ├── favicon.svg
│   ├── icon-192.png        PWA install icon
│   ├── icon-512.png        PWA splash screen
│   ├── avatars/            8 avatar options + custom upload
│   └── mascot/             Dicomi mascot expressions
└── manga/                  Placeholder for user-uploaded manga covers
```

### How it fits together

**Browser load → index.html** boots `app.js`, which:
1. Loads manga dataset from `data.js` and parses URL query params
2. Initializes Supabase auth listener (`auth.js`) to restore sessions
3. Renders manga grid with search, filters, genre dropdowns
4. Handles user interactions: favorites, read-lists, custom collections, ratings
5. Opens modals for auth, chat, quiz, advanced filters, manga detail
6. Saves state to `localStorage` immediately; syncs to Supabase in background (`sync.js`)

**Detail page (manga.html)** loads `manga.js`, which:
- Fetches manga by URL param; renders title, cover, metadata, volume gallery
- Loads comments from Supabase; allows authenticated users to reply with emoji/GIF reactions
- Tracks "last viewed" and view counts in stats

**Service Worker (sw.js)** intercepts fetch requests:
- **Images** (covers, portraits): Cached → serve instantly; revalidate in background
- **HTML/JS/CSS**: Network-first → always fresh online; fallback to cache offline
- **Cross-origin** (Supabase, CDN): Bypassed — no caching

## How to run it

This is a **static site** — no build step needed. Open `index.html` in a browser or deploy to any web server.

### Local development (with Service Worker):
```bash
# Start a simple HTTP server
python -m http.server 8000
# or
npx http-server -c-1
# or
php -S localhost:8000

# Open http://localhost:8000
```

### Deploy to production:
Push to GitHub → set up GitHub Pages on branch `main` (or set CNAME to dicomanga.com).

### Environment (Supabase):
No `.env` needed — credentials are in `js/supabase-config.js` (safe for public keys).  
Update these constants if using a different Supabase project:
```javascript
const SUPABASE_URL = 'https://YOUR-PROJECT.supabase.co';
const SUPABASE_ANON_KEY = 'YOUR-ANON-KEY';
```

### First-time user workflow:
1. Browse manga → search/filter/sort
2. Click manga card → detail page with comments
3. Add to favorites ⭐ or "À lire" 📚 (persisted locally)
4. Click "Connexion" to sign up/log in → sync data to cloud
5. Access stats, chat, custom lists, achievements

## Try asking

- **How do I add a new manga to the database?**  
  Edit `js/data.js` — each entry needs `id`, `titre` (title), `auteur` (author), `genre` (array), `volumes`, `couverture` (cover URL), `annee` (year), `statut` (status), `note` (rating out of 10), and `resume` (summary).

- **How do badges/achievements work and where do I unlock them?**  
  See `app.js` lines 3467–3481 for the achievement conditions (e.g., favorites ≥10 = "Collector", ratings ≥25 = "Expert Critic", streak ≥7 days = "Dedicated"). Badges are checked on page load and stored in `localStorage['unlockedBadges']`.

- **Can I customize the color scheme?**  
  Yes! Settings menu (⚙️) → "Studio couleurs" → pick a preset color (red/pink/blue/etc.) or input custom accent & background hex values. Theme is saved to `localStorage['customTheme']` and applied via CSS custom properties.

- **What happens to my data if I don't sign in?**  
  Everything stays on your device (browser storage). Favorites, read-lists, ratings, and notes are in `localStorage`. Sign in to sync to Supabase and access from any device. No account required for offline use.

- **How is the release calendar built for 2026?**  
  `js/calendar-2026.js` contains hardcoded manga release dates as an array of objects. `js/calendar.js` renders the month view. Data is static — no API.
