[README.md](https://github.com/user-attachments/files/34360765/README.md)
# ESP32 · Blynk + GitHub Pages Dashboard

Live IoT monitor for your ESP32 project — controlled via Blynk, data displayed
on a self-hosted GitHub Pages dashboard.

---

## What's in this repo

| File | Purpose |
|---|---|
| `BLynk_Connect_v2.ino` | Arduino sketch (upload to ESP32) |
| `index.html` | GitHub Pages dashboard |
| `README.md` | This file |

---

## Step 1 — Create the GitHub Gist

1. Go to <https://gist.github.com>
2. Create a **secret** gist
3. Add one file named exactly: `esp32_data.json`
4. Put any placeholder content (e.g. `{}`) and save
5. Copy the **Gist ID** from the URL:
   `https://gist.github.com/YOUR_USERNAME/`**`abc123def456`**

---

## Step 2 — Create a GitHub Personal Access Token (PAT)

1. Go to <https://github.com/settings/tokens>
2. Click **Generate new token (classic)**
3. Give it the **`gist`** scope only
4. Copy the token — you will not see it again

---

## Step 3 — Configure the Arduino sketch

Open `BLynk_Connect_v2.ino` and fill in your values:

```cpp
char ssid[] = "YOUR_WIFI_SSID";
char pass[] = "YOUR_WIFI_PASSWORD";

const char* GITHUB_TOKEN = "ghp_xxxxxxxxxxxxxxxxxxxx"; // PAT from Step 2
const char* GIST_ID      = "abc123def456";              // Gist ID from Step 1
```

Your Blynk credentials are already set at the top of the file.

---

## Step 4 — Configure the dashboard

Open `index.html` and set the Gist ID and license plate in the JavaScript config block:

```js
const GIST_ID      = "abc123def456";   // Same Gist ID
const PLATE_NUMBER = "WXY 1234";       // Fixed plate shown on the dashboard
```

Open the dashboard and click **ENABLE ALERT SOUND** once (browsers block audio
until you interact with the page). When the ESP32 combo/music trigger fires,
the dashboard shows a flashing warning banner and plays a siren.

---

## Step 5 — Deploy the dashboard to GitHub Pages

1. Create a new GitHub repository (public)
2. Push `index.html` (and optionally `README.md`) to the `main` branch
3. Go to **Settings → Pages → Source** → select `main` branch, `/ (root)`
4. Your dashboard will be live at:
   `https://YOUR_USERNAME.github.io/YOUR_REPO_NAME/`

---

## Step 6 — Upload to ESP32

Install the following libraries in Arduino IDE (Library Manager):

- **Blynk** by Volodymyr Shymanskyy
- **DFRobotDFPlayerMini** by DFRobot
- **HTTPClient** (built in with ESP32 board package)

Select board: **ESP32 Dev Module**

Upload `BLynk_Connect_v2.ino` and open Serial Monitor at 115200 baud.

---

## Blynk App — Virtual Pin Setup

| V-Pin | Widget | Label | Notes |
|---|---|---|---|
| V1 | Button | Static LED | Mode: Switch |
| V2 | Button | Blink Override | Mode: Switch |
| V3 | Button | Music Play | Mode: Switch |
| V4 | Slider | Blink Speed | Min 10, Max 500, step 10 |
| V5 | Slider | Volume | Min 0, Max 30 |
| V6 | LED | Switch LED 1 | Read-only status |
| V7 | LED | Switch LED 2 | Read-only status |
| V8 | LED | Switch LED 3 | Read-only status |
| V9 | LED | Is Playing | Read-only status |
| V10 | LED | Button Combo | Read-only status |

---

## How it works

```
Physical buttons/switches
        │
        ▼
   ESP32 (loop)
        │
   ┌────┴─────────────────────────────┐
   │                                  │
   ▼                                  ▼
Blynk cloud                   GitHub Gist (PATCH every 30 s + instantly on trigger)
(real-time V-pins)                    │
                                      ▼
                             GitHub Pages dashboard
                             (fetches Gist every 60 s)
```

- Blynk gives you **real-time control** (sub-second response)
- The Gist + dashboard gives you a **public shareable view** of current state

---

## Wiring reference

| Component | GPIO |
|---|---|
| Blink LED A | 12 |
| Blink LED B | 14 |
| Static LED | 13 |
| Switch 1 | 27 → LED GPIO 33 |
| Switch 2 | 26 → LED GPIO 32 |
| Switch 3 | 25 → LED GPIO 23 |
| Button CH6 | 34 |
| Button CH3 | 39 |
| Button CH0 | 36 |
| DFPlayer RX | 17 |
| DFPlayer TX | 16 |
