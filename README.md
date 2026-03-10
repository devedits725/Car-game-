# Car-game-
# 🏎 AE86 Touge Racer

A browser-based 3D racing game where your **phone is the steering wheel**. Tilt your phone to steer the Toyota AE86 Sprinter Trueno around an Akina-inspired touge circuit. Control throttle and brake with a DualShock 4 controller. Runs entirely in the browser — no app install required.

---

## How It Works

| Device | Role |
|--------|------|
| 📺 TV / PC Browser | Renders the 3D game (`/`) |
| 📱 Phone Browser | Gyroscope steering controller (`/phone`) |
| 🎮 DualShock 4 | Throttle (R2) + Brake (L2) |

The phone sends gyroscope tilt data to the TV in real-time over **Ably** (WebSocket, ~50ms latency). The DS4 connects via Bluetooth directly to the TV/PC.

---

## Project Structure

```
gyro-racer-vercel/
├── tv.html              # 3D game — open on TV/PC
├── phone.html           # Gyro controller — open on phone
├── vercel.json          # Routes: / → tv.html, /phone → phone.html
└── assets/
    ├── scene.gltf       # AE86 3D model
    ├── scene.bin        # Model geometry data
    └── textures/        # 26 PBR texture maps
```

---

## Setup & Deployment

### 1. Get a Free Ably API Key

1. Sign up at [ably.com](https://ably.com) (free tier is enough)
2. Create an App → go to **API Keys**
3. Copy the **Root key** — it looks like `xVLRLA.abc123:def456xyz` (contains a colon)

### 2. Paste the Key into Both Files

Open `tv.html` and `phone.html` and replace the placeholder in each:

```js
const ABLY_KEY = 'PASTE_ABLY_API_KEY_HERE';
//               ↑ replace with your key in BOTH files
```

### 3. Deploy to Vercel

1. Go to [vercel.com](https://vercel.com) and create a free account
2. Click **Add New Project** → **Upload** the zip
3. No build settings needed — it's static HTML
4. Vercel gives you a URL like `https://your-project.vercel.app`

> You must deploy to HTTPS. Gyroscope access (`DeviceOrientationEvent`) is blocked on plain HTTP.

---

## Playing the Game

### Step 1 — Open the TV page
Navigate to `https://your-project.vercel.app` on your TV or PC in Chrome.

### Step 2 — Connect the DS4 (optional but recommended)
1. Hold **PS + Share** on the controller until the light bar flashes
2. Pair it via Bluetooth on your PC/TV
3. Press any button — the HUD shows **🎮 DS4 CONNECTED ✓**

### Step 3 — Connect your phone
1. Open `https://your-project.vercel.app/phone` on your phone
2. Tap **STEP 1 — CONNECT**
3. **iOS only:** tap **STEP 2 — ENABLE GYRO** (required by Apple)
4. The TV HUD shows **📱 PHONE CONNECTED ✓**

### Step 4 — Race
- Press **▶ START RACE** on the TV (or press **✕** on the DS4)
- A **3… 2… 1… GO!** countdown begins
- Tilt your phone left/right to steer
- Hold **R2** to accelerate, **L2** to brake

---

## Controls Reference

| Input | Action |
|-------|--------|
| Tilt phone left / right | Steer |
| DS4 R2 (analog) | Accelerate |
| DS4 L2 (analog) | Brake |
| DS4 ✕ (Cross) | Start race from title screen |
| Arrow keys / WASD | Keyboard fallback (no phone/controller) |

### Phone Controller Buttons

| Button | Function |
|--------|----------|
| ⇄ FLIP | Invert steering direction |
| ✦ RECAL | Re-calibrate centre position |
| ↻ AXIS | Switch tilt axis (Gamma / Beta / Alpha) |
| ⚙ SENS | Adjust steering sensitivity (8°–60°) |

---

## Game Features

- **Akina-inspired touge circuit** — sweeping rights, hairpins, and an S-section built with CatmullRomCurve3
- **Real AE86 model** — 1985 Toyota Sprinter Trueno with full PBR textures (cabin, tyres, rims, lights, engine)
- **Lap timer & best lap** — live stopwatch, gold highlight on personal best
- **3-lap race** — finish screen shows your best lap time
- **Drift indicator** — ⚡ DRIFT appears when holding a hard angle at speed
- **Armco barriers** — alternating red/white rails with collision bounce
- **Start/finish gantry** — checkerboard road decal + overhead beam
- **Environment** — gradient sky shader, mountains, trees, drifting clouds, ACES filmic tonemapping

---

## Troubleshooting

**Phone screen keeps sleeping**
The app uses the Wake Lock API with automatic re-acquisition. If your screen still sleeps, go to your phone's display settings and increase the screen timeout to the maximum while playing.

**iOS gyro not working**
Go to **Settings → Safari → Motion & Orientation Access** and make sure it's **ON**. Then reload the phone page and tap Step 2 again.

**"CONNECTION FAILED" on phone**
Your Ably key is missing or incorrect. Double-check that you pasted the same key into `phone.html` (not just `tv.html`). The key must contain a colon (`:`).

**Car model not showing**
The browser falls back to a simple box-car if the GLTF fails to load. This usually means the `/assets/` folder wasn't deployed. Make sure `scene.gltf`, `scene.bin`, and the `textures/` folder are all included in your Vercel deployment.

**DS4 not detected**
The Web Gamepad API works best in **Chrome**. Safari and Firefox have limited support. Make sure the controller is paired via Bluetooth before opening the page.

**High latency / lag on steering**
Ably free tier supports 50 messages/second. The phone sends ~20/sec, well within limits. If you see lag, check your phone's Wi-Fi signal — the phone and TV should be on the same network for lowest latency.

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| 3D Rendering | [Three.js r128](https://threejs.org) |
| Car Model | Three.js GLTFLoader |
| Real-time comms | [Ably](https://ably.com) WebSocket |
| Gyroscope | Web DeviceOrientation API |
| Controller | Web Gamepad API |
| Hosting | [Vercel](https://vercel.com) (static) |

---

## Ably Free Tier Limits

The free tier is more than enough for this game:

| Limit | Free Tier | This Game |
|-------|-----------|-----------|
| Messages / second | 50 | ~20 |
| Concurrent connections | 100 | 2 |
| Monthly messages | 6 million | ~432,000 / hour of play |

---

*Built with Three.js, Ably, and the Web Gamepad API. No server required.*
