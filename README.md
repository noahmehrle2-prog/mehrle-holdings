# Mehrle Holdings LLC — company website

A self-contained static site whose **main job is to satisfy Apple's organization-migration requirement**:
> *"A valid organization website is required. Your organization's website must be publicly available and the domain name must be associated with your organization."*

It also doubles as the real public site for the company.

## Files
```
index.html     Landing page (company, values, apps, about, contact)
privacy.html   Company privacy policy (points to per-app policies)
terms.html     Terms of use
404.html        Not-found page
robots.txt      Allow all crawlers
sitemap.xml     Three URLs
```
No build step, no dependencies, no JavaScript, no external requests (fonts are system, favicon is inline SVG). Drop the folder on any static host and it works.

---

## Step 0 — Start your D-U-N-S number NOW (it gates everything and has lead time)
Apple verifies the LLC against a **D-U-N-S number** — a free business identifier from Dun & Bradstreet. The migration **cannot complete without it**, and a new one can take **up to ~5 business days** to issue (plus up to ~2 more for Apple to sync), so start this before anything else.
- Get or look up a free number at **developer.apple.com/enroll/duns-lookup** (or dnb.com).
- Write down the **exact** legal name and address on the D&B record. Everything downstream — the LLC's formation documents, the domain registrant, the App Store Connect entity name, and your message to Robert — must match it **character-for-character** (`Mehrle Holdings LLC` vs `Mehrle Holdings, L.L.C.` vs a trailing period are all *different* to Apple). Name/address mismatch is the #1 cause of stalled migrations.

## Step 1 — Buy the domain
Register **`mehrleholdingsllc.com`**. (The site is already written for that exact domain — canonical tags, OG tags, sitemap, and the `hello@mehrleholdingsllc.com` email all assume it.)
- Use **Cloudflare Registrar**, **Porkbun**, or **Namecheap** (~$10–12/yr).
- **Register it in the LLC's name** if the registrar allows (Mehrle Holdings LLC as the registrant org). This is the single strongest "domain associated with the organization" signal for Apple.
- If `.com` is taken: `mehrleholdings.co`, `mehrle.dev`, or `mehrleholdings.app` are fine — but then **find-and-replace `mehrleholdingsllc.com`** across all files first.

## Step 2 — Deploy (pick one)
**Netlify (easiest — you've used it before):**
1. app.netlify.com → "Add new site" → "Deploy manually".
2. Drag the **`mehrle-holdings/` folder** onto the drop zone. Live in ~10 seconds on a `*.netlify.app` URL.
3. Site settings → Domain management → add custom domain `mehrleholdingsllc.com` → follow Netlify's DNS instructions (either point your registrar's nameservers at Netlify, or add the CNAME/A records it shows). HTTPS is automatic.

**GitHub Pages (free alt):** push these files to a repo → Settings → Pages → deploy from branch → add a `CNAME` file containing `mehrleholdingsllc.com` → set the same DNS at your registrar.

**Cloudflare Pages (free alt):** connect repo or direct-upload; add the custom domain in the dashboard.

## Step 3 — Set up the email address (strengthens org association)
Make `hello@mehrleholdingsllc.com` actually work — Apple may email it, and a domain-matched contact reinforces ownership.
- Most registrars (Cloudflare, Porkbun, Namecheap) offer **free email forwarding**. Forward `hello@mehrleholdingsllc.com` → `noah.mehrle2@gmail.com`.
- **Send a test message** to `hello@mehrleholdingsllc.com` and confirm it arrives (and isn't in spam). A bouncing address is *worse* than none — Apple/D&B may email it to corroborate ownership.
- **Why it matters:** Apple cross-checks the website domain against the **contact/work email on your developer account**. Once the domain is live, set your App Store Connect account contact to `hello@mehrleholdingsllc.com` (forwarding to your Gmail) so the domains match. A full mailbox / Workspace is optional — forwarding is enough.

## Step 4 — Verify it's live
Open in a browser and confirm:
- `https://mehrleholdingsllc.com` loads with a padlock (valid HTTPS).
- "Mehrle Holdings LLC" is clearly visible.
- `/privacy.html` and `/terms.html` resolve.
- It loads on your phone too (it's responsive).

---

## Step 5 — Edit before you ship (quick find-and-replace)
The site uses sensible defaults; confirm or change these:

| Placeholder | Currently | Confirm |
|---|---|---|
| Legal entity name | **Mehrle Holdings LLC** | ✅ as given |
| State of organization | **Ohio** (footer + terms governing law) | ⚠️ change if the LLC is registered in another state |
| Location | **Cincinnati, Ohio · USA** | ⚠️ edit if wrong (no street address is shown, by design) |
| Founder | **Noah Mehrle** (About section) | ✅ |
| Founded | **2026** | ⚠️ set to the LLC's formation year |
| Contact email | **hello@mehrleholdingsllc.com** | set up forwarding (Step 3) — or swap to your Gmail |

> **No personal address, DOB, or phone is on the site, on purpose.** The only public contact is the company email.

---

## Apple migration checklist — all 6 of Robert's points

**Robert (Apple) said the migration can start at any time. Here's the state of each requirement and what *you* still need to do.**

1. **Two-factor auth on your Apple Account** — **You must confirm.** Go to your Apple ID settings → Sign-In & Security → Two-Factor Authentication = On. (Almost certainly already on, since it's required to use App Store Connect — but Apple lists it, so verify.)

2. **Valid organization website on a domain associated with the org** — **This repo + Steps 1–4.** Once `mehrleholdingsllc.com` is live with the LLC name on it and (ideally) registered to the LLC, this is satisfied. *This was the actual blocker.*

3. **Certificates, Identifiers & Profiles portal is unavailable during migration** — **Sequencing matters.** App Store Connect stays usable, but you can't create/edit certs or provisioning profiles while the migration runs.
   - ✅ Build 191 is already uploaded, signed, and in TestFlight, and a fresh App Store provisioning profile (`Pour AppStore b191 AD`) already exists — so signing is done.
   - 👉 **Recommended order:** *finish submitting Pour for App Store review (attach build 191 → submit) **before** you tell Robert to start the migration.* If a reviewer rejects and you need a NEW signed build mid-migration, you'd be locked out of the cert portal until it finishes. Submitting first shrinks that exposure window (though an in-review rejection mid-migration still can't be re-signed until it completes). (TestFlight beta itself doesn't need cert changes, so external testing can continue.)
   - 👉 **Before migration starts**, confirm your distribution certificate and provisioning profiles are current (not near expiry) and that you don't need any new identifiers or test devices — the Certificates/Identifiers/Profiles portal is read-blocked the whole time.

4. **Your legal entity name applies to all apps after migration** — informational. After it completes, the App Store "seller" name flips from your personal name to **Mehrle Holdings LLC** on every app. This is the goal. Nothing to do.

5. **Individual Sales & Trends reports disappear after migration** — informational. Pour isn't generating sales yet, so there's nothing to lose **today**. ⚠️ Tied to point 6: *if* you launch paid IAPs before the migration completes, export Sales & Trends to CSV **before** starting — the individual report history goes away afterward.

6. **Pre-migration earnings go to the bank account active when migration starts** — informational. No earnings yet (app not live). If you launch paid IAPs before the migration finishes, early revenue routes to whatever bank account is on the account when migration begins — set/confirm banking in *Agreements, Tax, and Banking* **before** starting if you care where it lands.

### What to reply to Robert
**Confirm ALL of these first — each is a hard prerequisite, not a nicety:**
- [ ] D-U-N-S number issued; exact legal name + address on it confirmed (Step 0)
- [ ] `https://mehrleholdingsllc.com` loads live over valid HTTPS — test **both** the apex and `www`
- [ ] Domain registrant is **Mehrle Holdings LLC** (or you can show a registrar receipt proving LLC ownership)
- [ ] `hello@mehrleholdingsllc.com` received your test email
- [ ] Pour's App Store submission sequencing decided (point 3)
- [ ] The legal-entity-name string below **exactly** matches the D-U-N-S/formation record (punctuation + suffix included)

> "Thanks Robert. Two-factor auth is on, and our organization website is live at https://mehrleholdingsllc.com (domain registered to Mehrle Holdings LLC). Please go ahead and start the migration of my individual membership to the organization membership. Legal entity name: **Mehrle Holdings LLC**. D-U-N-S number: [your number]."

---

## Notes
- Independent of Pour's product domain (`getpour.app`), which keeps its own privacy/terms/support pages. This is the *company* site.
- Verified self-contained: no external CSS/JS/font requests, valid HTML, responsive, light + dark mode.
