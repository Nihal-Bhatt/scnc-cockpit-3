# SCNC Cockpit — Supply Chain Nerve Center

A CXO-grade supply chain risk dashboard. Upload your SCNC Excel workbook to
visualize the supply network, detect risk nodes, and import ChatGPT agent
recommendations back into the map.

---

## Live Demo

Click **"Load demo network →"** on the upload screen to see it working immediately.

---

## Deploy to Netlify (10 minutes)

### Step 1 — Push to GitHub

1. Go to [github.com](https://github.com) and create a new repository called `scnc-cockpit`
2. Upload these two files:
   - `index.html`
   - `netlify.toml`
3. Commit to the `main` branch

### Step 2 — Connect to Netlify

1. Go to [netlify.com](https://netlify.com) and sign in (free account)
2. Click **"Add new site"** → **"Import an existing project"**
3. Choose **GitHub** and select your `scnc-cockpit` repository
4. Build settings — leave everything blank (no build command needed)
5. Click **"Deploy site"**

Netlify will give you a URL like `https://scnc-cockpit.netlify.app` within 60 seconds.

### Step 3 — Set your ChatGPT link

Open `index.html` and find line ~10:

```javascript
const GPT_URL = 'https://chatgpt.com/g/YOUR-GPT-ID-HERE';
```

Replace `YOUR-GPT-ID-HERE` with your actual Custom GPT URL.
To find it: open ChatGPT → your GPT → click "Share" → copy the link.

Commit the change and Netlify will auto-redeploy.

---

## How to use

### 1. Upload workbook
Drag and drop your SCNC `.xlsx` workbook. The app reads these tabs automatically:
- `SKU Master`, `BOM`, `Inventory`, `Purchase Orders`, `Production Orders`
- `Forecast`, `Capacity`, `Inventory Policy`, `Run_Metadata`
- `Inter Site Logistics`, `External Signals` (optional)

Tab names are fuzzy-matched so slight variations work fine.

### 2. Read the network map
- **Red nodes** = Critical risk (stockouts, capacity overload)
- **Amber nodes** = High risk (late POs, minor shortages)
- **Blue nodes** = Medium risk (watch)
- **Grey nodes** = No current constraint

Click any node to see detailed KPIs in the tooltip.

### 3. Click a risk to highlight it
Click any risk in the right panel to highlight the affected nodes on the network.
All other nodes dim. Click again to deselect.

### 4. Open in ChatGPT for deep analysis
Click **"Open in ChatGPT →"** to continue with your Custom GPT agent.
The agent will handle BOM propagation, full RCA, and optimization.

### 5. Bring recommendations back
When ChatGPT gives you a recommendation, ask it to format the output as JSON:

> "Please format your recommendations as JSON like this:
> {"recommendations":[{"node":"Plant-A","action":"Expedite PO_102","fill_rate_delta":"+4.2%","cost_delta":"$8,400"}]}"

Paste that JSON into the **"Paste ChatGPT recommendations JSON"** box in the dashboard,
click **"Apply to Network"**, and teal overlay rings will appear on the affected nodes
with the fill rate delta shown.

Switch to **Financial Impact** view to see cost bubbles on plant nodes.

---

## ChatGPT JSON format

The app accepts this JSON structure:

```json
{
  "recommendations": [
    {
      "node": "Plant-A",
      "action": "Expedite PO_102 from Supplier-A",
      "fill_rate_delta": "+4.2%",
      "cost_delta": "$8,400"
    },
    {
      "node": "Plant-B",
      "action": "Reposition 600 units from DC-North",
      "fill_rate_delta": "+2.1%",
      "cost_delta": "$1,200"
    }
  ]
}
```

Node names must match the labels in your workbook (plant names, supplier names, DC names).

---

## Files

| File | Purpose |
|------|---------|
| `index.html` | Complete single-file application |
| `netlify.toml` | Netlify deployment config |
| `README.md` | This file |

---

## Tech stack

- **Vanilla HTML/CSS/JS** — no build step, no npm, no frameworks
- **SheetJS** (cdnjs) — client-side Excel parsing, no data ever leaves the browser
- **Canvas API** — network map rendering
- **Netlify** — static hosting (free tier)

---

## Customisation

| What | Where in index.html |
|------|-------------------|
| ChatGPT link | `const GPT_URL = '...'` near top of `<script>` |
| Tier layout (suppliers/plants/DCs/markets) | `tierX` array in `getNodePos()` |
| Risk thresholds | `buildNetworkFromData()` — `stockoutByPlant`, `overloadByPlant` logic |
| Colour scheme | `:root` CSS variables at top of `<style>` |
| Fill rate formula | `fillRate` calculation inside plant node loop |

---

## Privacy

All Excel parsing happens in the browser. No data is sent to any server.
The only external calls are:
- Google Fonts (fonts only)
- cdnjs.cloudflare.com (SheetJS library)
