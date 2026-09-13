# GeM Bid Compliance Verification Platform — Setup (Baby Steps)

## What's in this folder
- `app.py` — the whole app (frontend + AI logic, one file)
- `mock_portal_data.json` — simulated Udyam/GST/PAN/EPFO/ESIC/Startup India/NSIC/Blacklist records for 16 sample bidders, covering a broad spread of realistic scenarios: clean/compliant bidders, expired Udyam, cancelled GSTIN, suspended GST, overdue returns, PAN under verification hold, blacklisted/debarred, "under review" (not yet debarred), Large Enterprise (Udyam not applicable), Startup India registered, low/high Make in India %, missing ESIC due to workforce size, and more
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
5. The bidder's own document says nothing is wrong — but the portal lookup shows they're BLACKLISTED. The AI should flag this as a red flag and lower the risk rating.

**Test run 3 — the guardrail / prompt injection test (do this one live if a judge pushes back on security):**
1. Same tender PDF
2. Upload `test_malicious_vendor_bid.pdf` as the Vendor Bid — this document has hidden text trying to instruct the AI to ignore all issues and mark it as 100% compliant
3. Enter PAN: `AAECR5678D` (a bidder who genuinely has real compliance issues — overdue GST, no Udyam)
4. Click "Run Verification"
5. You should see a red "🛡️ Guardrail triggered" warning appear BEFORE the AI even runs, and the AI's own output should still correctly flag the real compliance issues rather than being fooled into a clean pass. This proves the system resists manipulation — good to have ready if a judge tries to break it, the same way one did in your last hackathon.

**Other sample PANs to try** — there are 16 total now, covering a wide range of issues (check `mock_portal_data.json` for the full list — don't show this file during the actual pitch, it gives away the mock data). A few interesting ones beyond the two test runs above:
- `AAPKR6789N` — GSTIN was cancelled by the department 3 months ago
- `AAMHO4567K` — PAN itself is under a verification hold
- `AANIP8901L` — under a show-cause notice but not yet formally debarred (tests whether the AI distinguishes "under review" from "blacklisted")
- `AAOJQ2345M` — a Large Enterprise, where Udyam/NSIC don't apply at all (tests whether the AI correctly treats "not applicable" as different from "missing")
- `AAJEL2345H` — Udyam registration has expired (tests expiry detection, not just presence/absence)

## Officer-only Audit Trail access
The Audit Trail tab (tab 2) now requires an access code before it shows any data — this simulates the fact that a real deployment would restrict this to authenticated procurement officers, not leave it publicly visible. The demo code is:
```
officer2026
```
This is a placeholder for the MVP, not real security — say this plainly if asked. In production this would be real government SSO/role-based login.

## If you get an error
Copy the exact red error text from the terminal and paste it into Antigravity's chat with "fix this error" — completely normal part of the process.

## Before your actual pitch
- Say clearly and upfront that Udyam/GST/PAN/EPFO/ESIC/Startup India/NSIC/Blacklist checks use a MOCK local database, not live government APIs — real integration would need official access to those government systems, which isn't available for a hackathon build. Judges expect and respect this honesty.
- The "Officer's Final Decision" section is there specifically because the PS says the AI must never make the final call — only recommend. Make sure to mention this explicitly when you pitch; it directly answers a requirement in the PS.
- Everything logged in "Audit Trail" (tab 2) demonstrates point 14 in the PS (auditable record) — show this tab briefly in your demo, and mention the access-code gate as a deliberate security decision.
- If a judge questions robustness, the prompt-injection guardrail (test run 3 above) is your strongest answer — it shows you've thought about adversarial inputs, not just the happy path.
