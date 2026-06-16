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

## Step 1 — Domain ✅ DONE
Bought **`mehrleholdingsllc.com`** on **GoDaddy**. The site is written for exactly this domain (canonical/OG/sitemap/email).
- ⚠️ **Registrant = the LLC:** in GoDaddy → domain → Contacts, make sure the registrant *organization* is **Mehrle Holdings LLC** (not just "Noah Mehrle"). This is the strongest "domain associated with the org" signal Apple checks. GoDaddy's free WHOIS privacy is fine, but the underlying record should name the LLC.

## Step 2 — Hosting ✅ DONE (GitHub Pages — free, auto-HTTPS)
Deployed to GitHub Pages: repo **github.com/noahmehrle2-prog/mehrle-holdings**, custom domain `mehrleholdingsllc.com` set via the `CNAME` file. Built and serving; it just needs DNS pointed at GitHub.

**Point GoDaddy DNS at GitHub Pages** — GoDaddy → My Products → `mehrleholdingsllc.com` → DNS → Manage DNS:
1. Delete GoDaddy's default Parked `A @` record and the `CNAME www → @` record.
2. Add **four A records**, Host `@`, Value (one each):
   `185.199.108.153` · `185.199.109.153` · `185.199.110.153` · `185.199.111.153`
3. Add **one CNAME**, Host `www`, Value `noahmehrle2-prog.github.io`.
4. Save. Propagates in ~10 min–few hours; GitHub then auto-issues the HTTPS cert. Then turn on "Enforce HTTPS" (repo → Settings → Pages).

## Step 3 — Email: make `hello@mehrleholdingsllc.com` work
GoDaddy no longer bundles free forwarding, so use **ImprovMX** (free, keeps DNS at GoDaddy):
1. At improvmx.com, add `mehrleholdingsllc.com` and create alias `hello@` → `noah.mehrle2@gmail.com`.
2. In GoDaddy DNS add: `MX @ → mx1.improvmx.com` (priority 10), `MX @ → mx2.improvmx.com` (priority 20), `TXT @ → v=spf1 include:spf.improvmx.com ~all`.
3. Send a test to `hello@mehrleholdingsllc.com` and confirm it lands (not spam). A bouncing address is *worse* than none — Apple/D&B may email it.
(Alternatives: GoDaddy/Microsoft 365 mailbox ~$2/mo, or Cloudflare Email Routing if you move DNS to Cloudflare.)
- **Why it matters:** Apple cross-checks the website domain against your developer-account contact email — set ASC's account contact to `hello@mehrleholdingsllc.com` once it works.

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
| Location | **Ohio · USA** | ⚠️ edit if wrong (no street address is shown, by design) |
| Founder | **Noah Mehrle** (About section) | ✅ |
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
