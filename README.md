# GeM Bid Compliance Verification Platform — Setup (Baby Steps)

## What's in this folder
- `app.py` — the whole app (frontend + AI logic, one file)
- `mock_portal_data.json` — simulated Udyam/GST/PAN/EPFO/ESIC/Startup India/NSIC/Blacklist records for 4 sample bidders
- `sample_tender.pdf` — a ready-made sample tender document to test with
- `sample_vendor_bid_clean.pdf` — a bidder who passes most checks (Unity Steel Traders, PAN: AABCU1234C)
- `sample_vendor_bid_risky.pdf` — a bidder who's actually BLACKLISTED in the mock data even though their own bid document doesn't mention it (Prakash Metal Works, PAN: AAGCP3456F) — this is your best demo moment, showing the AI catching what the bidder didn't disclose
- `requirements.txt` — packages needed

## How to run it

1. **Open this folder in VS Code or Antigravity.**

2. **Open a terminal** inside the editor.

3. **Install packages:**
   ```
   pip install -r requirements.txt
   ```

4. **Get a free Gemini API key:**
   - Go to aistudio.google.com and sign in with any personal Google account
   - Click "Get API key" → "Create API key" → copy it
   - This is genuinely free (no card needed) — no trial-credit expiry like some other providers

5. **Add your key to the code:**
   - Open `app.py`
   - Find this line near the top:
     ```python
     API_KEY = "PASTE_YOUR_GEMINI_API_KEY_HERE"
     ```
   - Paste your real key between the quotes. Save (Ctrl+S).

6. **Run it:**
   ```
   streamlit run app.py
   ```
   A browser tab opens automatically.

## How to test it (do this exact sequence for your demo)

**Test run 1 — the "clean pass" case:**
1. Upload `sample_tender.pdf` as the Tender Document
2. Upload `sample_vendor_bid_clean.pdf` as the Vendor Bid
3. Enter PAN: `AABCU1234C`
4. Click "Run Verification"
5. You should see a high compliance score, Low risk, mostly green PASS statuses

**Test run 2 — the "catches what the bidder hid" case (your best demo moment):**
1. Same tender PDF
2. Upload `sample_vendor_bid_risky.pdf` as the Vendor Bid
3. Enter PAN: `AAGCP3456F`
4. Click "Run Verification"
5. The bidder's own document says nothing is wrong — but the portal lookup shows they're BLACKLISTED. The AI should flag this as a red flag and lower the risk rating. **This is the moment that proves the "why AI, not just manual document reading" argument** — a human skimming just the bid document would miss it, but cross-referencing the portal catches it.

**Other sample PANs to try** (check `mock_portal_data.json` for details): `AAECR5678D` (non-compliant income tax, no Udyam), `AAFCG9012E` (suspended GST, Startup India registered)

## If you get an error
Copy the exact red error text from the terminal and paste it into Antigravity's chat with "fix this error" — completely normal part of the process.

## Before your actual pitch
- Say clearly and upfront that Udyam/GST/PAN/EPFO/ESIC/Startup India/NSIC/Blacklist checks use a MOCK local database, not live government APIs — real integration would need official access to those government systems, which isn't available for a hackathon build. Judges expect and respect this honesty.
- The "Officer's Final Decision" section is there specifically because the PS says the AI must never make the final call — only recommend. Make sure to mention this explicitly when you pitch; it directly answers a requirement in the PS.
- Everything logged in "Audit Trail" (tab 2) demonstrates point 14 in the PS (auditable record) — show this tab briefly in your demo.
