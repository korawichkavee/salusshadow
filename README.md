# Salus Shadow — Web App

Check if any lat/lon point is in shade at a given time, with street-level imagery from Mapillary.

```
GitHub Pages (frontend)  ←→  FastAPI on Render/Railway (backend)
```

---

## Project Structure

```
salusshadow-app/
├── frontend/
│   └── index.html          ← Deploy to GitHub Pages
└── backend/
    ├── main.py             ← FastAPI app
    ├── requirements.txt
    └── salusshadow.py      ← Your existing file (copy here)
```

---

## 1. Backend — Deploy to Render (free)

### a) Prep the repo

Create a new GitHub repo (or a subfolder in your existing one) containing:
- `main.py`
- `requirements.txt`
- `salusshadow.py`

### b) Deploy on Render

1. Go to [render.com](https://render.com) → New → **Web Service**
2. Connect your GitHub repo
3. Settings:
   - **Runtime**: Python 3
   - **Build command**: `pip install -r requirements.txt`
   - **Start command**: `uvicorn main:app --host 0.0.0.0 --port $PORT`
4. Click **Deploy**

Render gives you a URL like: `https://salusshadow-api.onrender.com`

> ⚠️ **Free tier note**: Render free instances spin down after 15 min of inactivity. First request after sleep takes ~30s. Upgrade to a paid plan ($7/mo) to keep it awake, or use Railway which has a slightly more generous free tier.

### c) Test it

```
curl "https://your-app.onrender.com/shade?lat=42.3601&lon=-71.0589&timestamp=2025-07-29T14:00:00-04:00"
```

---

## 2. Frontend — Deploy to GitHub Pages

### a) Edit `index.html`

Open `frontend/index.html` and update two things:

1. **Mapillary token** (line ~170):
   ```js
   const MAPILLARY_TOKEN = "YOUR_MAPILLARY_CLIENT_TOKEN";
   ```
   Get a free token at [mapillary.com/developer](https://www.mapillary.com/developer)

2. **Default API URL** in the sidebar input — users can also set this in the UI.

### b) Push to GitHub Pages

Option A — root of repo:
```bash
# Put index.html at repo root, then enable Pages in repo Settings → Pages → Deploy from branch (main, /)
```

Option B — `docs/` folder:
```bash
mkdir docs && cp frontend/index.html docs/
# In repo Settings → Pages → Deploy from branch (main, /docs)
```

Your app is live at: `https://yourusername.github.io/your-repo/`

---

## 3. Connect frontend → backend

In the app's sidebar, paste your Render URL into the **Backend API URL** field:
```
https://salusshadow-api.onrender.com
```

Or hardcode it as the default value in `index.html`:
```js
<input id="api-url" ... value="https://salusshadow-api.onrender.com"/>
```

---

## Features

- 🗺 Leaflet map — click to drop pin or drag it
- 📍 Manual lat/lon input
- 🕐 Local datetime picker
- 🌳 Toggle tree shadows
- ☀️ Sun azimuth + elevation display
- 📊 Street shadow ratio bar
- 📷 Mapillary street-level imagery panel

---

## Local Development

```bash
# Backend
cd backend
pip install -r requirements.txt
uvicorn main:app --reload --port 8000

# Frontend — just open in browser
open frontend/index.html
# The sidebar API URL defaults to http://localhost:8000
```

---

## CORS

The backend allows all origins by default (`allow_origins=["*"]`).
For production, tighten it in `main.py`:

```python
allow_origins=["https://yourusername.github.io"],
```

---

## Credits

- Shadow calculations: [salusshadow.py](./backend/salusshadow.py) — OSMnx + pvlib + GeoPandas
- Maps: [Leaflet](https://leafletjs.com) + [CARTO](https://carto.com)
- Street imagery: [Mapillary](https://mapillary.com)
