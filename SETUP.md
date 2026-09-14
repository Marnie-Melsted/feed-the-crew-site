# Feed the Crew — Go-Live Checklist

Everything in this folder is real, working code. This checklist covers the handful of external account steps that only you can do (I can't create accounts on your behalf). None of it requires writing code — just clicking through dashboards and pasting values into settings boxes.

---

## 1. Stripe (payments) — ~15 minutes

1. Create a free account at stripe.com.
2. **Products → Add product**, create two:
   - "Home & Family" — $9/month recurring (and optionally a second $79/year price on the same product)
   - "Pro & Catering" — $39/month recurring (and optionally $349/year)
3. For each price, click **Create payment link**.
4. On each payment link's settings, set the **after payment** redirect to:
   `https://YOURSITE.netlify.app/app.html?session_id={CHECKOUT_SESSION_ID}`
   (Stripe fills in the real session ID automatically — leave that part exactly as shown.)
5. Copy the **Price ID** for your Pro & Catering monthly price (starts with `price_...`) — you'll paste this into Netlify in Step 3.
6. Go to **Developers → API keys** and copy your **Secret key** (starts with `sk_live_...` once you're out of test mode). Keep this private.

## 2. Netlify (hosting + the working backend) — ~15 minutes

1. Create a free account at netlify.com.
2. Easiest path for a non-coder: create a free GitHub account, create a new repository, and use GitHub's web upload button to drag this entire `site` folder in (no command line needed). Then in Netlify: **Add new site → Import an existing project → connect to GitHub → select the repository**.
   - (Plain drag-and-drop deploy on Netlify does *not* support the serverless functions in this folder — you need the GitHub-connected path so Netlify can build them.)
3. Once connected, go to **Site settings → Environment variables** and add:
   - `ANTHROPIC_API_KEY` — your Anthropic API key
   - `STRIPE_SECRET_KEY` — the Stripe secret key from Step 1
   - `STRIPE_PRICE_ID_PRO` — the Pro price ID from Step 1
   - `ACCESS_TOKEN_SECRET` — any long random string you make up (e.g., mash your keyboard for 40 characters) — this is what signs the access tokens, keep it private and never change it once live or everyone's existing token breaks
4. Trigger a deploy (Netlify does this automatically after connecting).
5. Your site is now live at `https://YOURSITE.netlify.app` (you can add a real custom domain later in Site settings → Domain management).

## 3. Wire the two together

1. Go back to Stripe and double check the Payment Link redirect URLs from Step 1 use your actual Netlify URL.
2. On `pricing.html`, update the two "Start with..." buttons to point to your real Stripe Payment Links instead of `app.html?plan=home` / `app.html?plan=pro`.

## 4. Test it for real before telling anyone

1. Use Stripe's test mode (toggle in the Stripe dashboard) and a test card number (4242 4242 4242 4242) to run through a full purchase.
2. Confirm you land back on `app.html`, the paywall disappears, and the tool loads.
3. On a Pro test purchase, confirm the AI receipt scanner and nutrition estimator actually work.
4. On a Home test purchase, confirm those two features are disabled with the "requires Pro" message instead of just silently failing.
5. Only then switch Stripe out of test mode and go live for real.

---

## What this setup does and doesn't cover (be honest with yourself here)

- **Covers**: real payment verification, a real working AI backend, and gating AI features to Pro subscribers only.
- **Doesn't cover**: full user accounts. Right now, access is tied to a token stored in that one browser for about 35 days — if someone switches browsers or clears their data, they'd need to go through Stripe's customer portal or contact you to restore access. This is a reasonable, honest v1. A real accounts system (so someone can log in from any device) is a natural next step once you have paying users and want to invest further.
- **Doesn't cover**: cancellation automatically revoking access mid-cycle, failed-payment handling, or a subscription-management page for customers. Stripe's own **Customer Portal** (free, one toggle to turn on in the Stripe dashboard) covers letting customers update payment methods and cancel — link to it from your Support page once you've enabled it.
