# 🚀 VeilPay Quickstart & How-to-Run Guide

This guide walks you through setting up, running, and verifying **VeilPay** locally in under 3 minutes.

---

## ⚡ Fast 3-Step Quickstart

### 1. Environment Setup

- **Windows (PowerShell)**:
  ```powershell
  python -m venv .venv
  .\.venv\Scripts\Activate.ps1
  pip install -r requirements.txt
  ```

- **macOS / Linux**:
  ```bash
  python3 -m venv .venv
  source .venv/bin/activate
  pip install -r requirements.txt
  ```

### 2. Configure Environment Variables

Copy the template:
```bash
# Windows
Copy-Item .env.example .env

# macOS / Linux
cp .env.example .env
```

Ensure `.env` contains your MongoDB Atlas URI, PayPal Sandbox credentials, and AI API Key:
```ini
FLASK_SECRET_KEY=dev-secret-key-change-in-production
MONGODB_URI=mongodb+srv://<user>:<password>@<cluster>.mongodb.net/veilpay?retryWrites=true&w=majority
RP_ID=localhost
RP_NAME=VeilPay
ORIGIN=http://localhost:5000
PAYPAL_CLIENT_ID=your_sandbox_client_id
PAYPAL_CLIENT_SECRET=your_sandbox_client_secret
PAYPAL_BASE_URL=https://api-m.sandbox.paypal.com
FERNET_KEY=your_generated_fernet_key
LLM_API_KEY=your_groq_or_gemini_api_key
LLM_MODEL=openai/gpt-oss-120b
DEMO_MODE=true
LOW_HOLD_SECONDS=60
MEDIUM_HOLD_SECONDS=60
INTENT_TTL_SECONDS=300
```

> **Note on AI Keys**: VeilPay works with **Groq** (`gsk_...` with `openai/gpt-oss-120b` or `llama-3.3-70b-versatile`) as well as **Google Gemini** (`gemini-1.5-flash`). Even if unconfigured, deterministic regex and fallback rules ensure 100% functionality.

---

### 3. Run Automated Tests

Run the full verification suite before starting:
```bash
pytest -v
```
**Expected Result**: All 27 tests pass (`100% passed`) covering privacy invariants, cryptographic signatures, replay defense, hold/void, and AI scrubbing.

---

### 4. Start VeilPay

```bash
python run.py
```
Open **`http://localhost:5000`** in Google Chrome, Microsoft Edge, or Safari.

---

## 🧪 Interactive Testing Checklist for Judges

Follow this 5-minute walkthrough to test every feature of VeilPay:

### 1. Register with Biometric Passkey
- Go to `http://localhost:5000/register`.
- Enter a friendly public display name (e.g. `apple`, `green`, `Tina`) and your sandbox email.
- Tap **Continue with fingerprint or face** to create a native WebAuthn passkey via Windows Hello, Touch ID, or security key.

### 2. Dashboard & Personal QR Code
- On `http://localhost:5000/dashboard`, review your virtual card showing your public name and unforgeable alias (`veil-xxxxxx`).
- Scroll to the bottom to find your **Personal QR Code Card**.
- Click **📥 Save QR Image** to download `veilpay-qr.png`.

### 3. Test QR Upload & Nickname Payments
- Go to `http://localhost:5000/pay`.
- **Option A (Saved Contacts)**: Click `⚡ Load Demo Friends` to instantly load `green`, `apple`, and `Bob`, then tap any friend's pill to select them.
- **Option B (Upload QR from PC/Gallery)**: Tap **📁 Upload QR (Gallery / PC)** and select any QR image or screenshot. Watch the image thumbnail appear and auto-populate the recipient!
- **Option C (Type Nickname)**: Type `green` — the system automatically matches and displays `✓ Match: green (veil-bi5s6j)`.

### 4. Live Privacy Shield (AI PII Scrubber)
- In the **Payment Memo** field, type a note with sensitive info:  
  `"Hey call me on 9876543210 or email me at user at gmail dot com"`
- Observe the real-time AI privacy badge detect the phone number and email and scrub it to `[hidden]` before transmission.

### 5. Review, Sign, and 60-Second Safety Hold
- Enter an amount (e.g., `$10.00`) and click **Review & Sign Payment →**.
- Verify the SHA-256 canonical hash of the transaction.
- Click **Sign with Passkey** (or Fast-Track Sandbox Demo).
- On the **Status Page** (`/status`), watch the **60-second Hold Window** countdown.
- Test clicking **"This was not me"** to void the authorization immediately.

### 6. Security Lab & Attack Simulator
- Navigate to `http://localhost:5000/demo`.
- Click **⚡ Attacker tries to pay without signature** → `🛑 BLOCKED_BY_POLICY`.
- Click **⚡ Attacker replays a signed payment** → `🛑 BLOCKED_BY_REPLAY_PROTECTION`.
- Click **⚡ Attacker edits the amount in transit** → `🛑 BLOCKED_BY_CRYPTOGRAPHIC_BINDING`.

---

## 🔧 Common Troubleshooting

| Issue | Resolution |
|---|---|
| **Passkey prompt doesn't show** | Ensure you are accessing via `http://localhost:5000` (WebAuthn requires `localhost` or HTTPS). |
| **MongoDB Atlas connection timeout** | Verify your IP address is whitelisted in MongoDB Atlas Network Access (`0.0.0.0/0` for testing). |
| **QR code camera inactive on laptop** | Use the **📁 Upload QR (Gallery / PC)** button to select a screenshot/photo directly, or click a 1-click quick demo button (`🟢 Scan green`). |
| **Browser showing older cached styles** | Press `Ctrl + Shift + R` (or `Ctrl + F5`) to force a hard reload of browser cache. |

