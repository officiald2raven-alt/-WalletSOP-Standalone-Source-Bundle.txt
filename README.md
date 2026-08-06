# WalletSOP

A free, open-source, 100% stateless security audit for cryptocurrency cold-storage procedures.

WalletSOP walks you through a structured questionnaire about how you set up and manage your hardware wallet — never what your seed phrase is — and produces a personalized, printable Standard Operating Procedure (SOP) for anything worth hardening. It's diagnostic education, not mathematical verification of any specific wallet or device.

No login. No tracking. No backend. Nothing you enter ever leaves your browser.

Why This Exists

Most funds lost from cold storage aren't lost to broken cryptography — they're lost to procedural mistakes: trusting a single source of randomness, skipping a passphrase, photographing a seed "just to check," or letting firmware go unpatched. Good security guidance for this exists, but it's scattered across forums, videos, and vendor docs, and there's rarely a clear moment to sit down and audit your own setup against it.

WalletSOP creates that moment. It asks about your procedure, scores it against documented best practices (BIP-39 entropy standards, standard hardware-wallet opsec), and hands back a checklist tailored to what's actually weak — not a generic list, and never a fabricated one.

Absolute Rules

These are non-negotiable design constraints, not just features:

Never asks for a seed phrase, private key, or any secret material.
Never flags a specific device model + date combination as "compromised." Every scoring rule maps to a documented, established best practice — nothing device-specific or invented.
Scoring is 100% deduction/addition based, fully disclosed in-app (see the rubric below).
Zero network requests. Zero backend. Zero data persistence, ever.
No live connection to any physical hardware wallet (no WebUSB/WebHID). This tool never touches your actual device.
Self-report only. The tool cannot and does not claim to verify anything you tell it — see What This Tool Is — and Isn't.
How It Works

WalletSOP scores four independent categories — not a single blended number, because they measure different kinds of risk:

Category	What it measures
Entropy	How your seed's randomness was generated
Backup & Storage	Whether your seed has ever been exposed to a digital/networked device
Access Control	Passphrase and multisig usage
Operational Hygiene	Firmware currency
Scoring Rubric (v1.0 — 2026-08-05)

Each sub-score starts at 100 and is calculated independently. Clamped 0–100.

A. Entropy

Condition	Points
Device TRNG only	−40 (Medium — Single Point of Failure)
Mixed (dice + device)	−15 (Medium — Single Point of Failure)
Dice-based, <20 rolls	+10
Dice-based, 20–49 rolls	+30
Dice-based, 50+ rolls	+60

B. Backup & Storage

Condition	Points
Seed has ever existed digitally (file, cloud, password manager, photo, typed on a networked device)	−100 (Critical — Fatal Failure)

C. Access Control

Condition	Points
BIP-39 passphrase in use	+30
Multisig quorum in use	+20
(Absence of either is shown as "points foregone," never a penalty.)	

D. Operational Hygiene

Condition	Points
Firmware current	+10
Firmware not current	−10

The full rubric is also viewable inside the app, collapsible under every audit result, tagged with its version and date so future changes stay traceable.

The Only Path to "Move Your Funds"

WalletSOP recommends fund migration in exactly one case: an explicit "yes" to having exposed your seed to a digital/networked device. Every other result produces a Hardening SOP — firmware updates, adding a passphrase, increasing entropy for future seeds — and explicitly states that existing funds do not need to move. No result is designed to manufacture urgency.

Features
Audit — the core questionnaire and scoring engine described above, with live, plain-language rationale under every answer and a "why this matters" explanation attached to every recommendation.
Entropy Calculator — a standalone tool to compute exact bits of entropy for your dice-rolling plan, compared against BIP-39's 128-bit (12-word) and 256-bit (24-word) thresholds. Never feeds into your audit score, and never accepts actual roll results — only how many rolls.
Best Practices — static reference material on manual entropy, passphrases, multisig, common seed-storage mistakes, and general opsec. No score, no inputs.
Seed Regeneration Guide — a dedicated, detailed walkthrough (not a single checklist line) whenever regenerating a seed is recommended: choosing fair dice, rolling without bias, verifying each roll on-device, and never letting software assist.
Printable SOP — export your personalized checklist, with completion checkboxes, for offline record-keeping.
Installable PWA — install WalletSOP to a home screen or desktop and run it fully offline, with no build step required. See Install as an App below.
What This Tool Is — and Isn't

This audit evaluates the procedures you report, not your actual device or wallet. It cannot detect malware, supply-chain tampering, or hardware-specific vulnerabilities — this tool never connects to your device. It is not a substitute for professional review, especially for large holdings. Your answers are self-reported and never verified against reality.

This statement appears inside the app itself, not just here.

Quick Start
bash
npm install
npm run dev
Run Fully Offline

Because WalletSOP is stateless and client-side only, you can verify the "zero network requests" claim yourself:

bash
npm run build

Then disconnect from the internet entirely and open dist/index.html directly in your browser, or serve the dist/ folder locally:

bash
npx serve dist

The app will function identically with no internet connection — every computation happens in your browser.

Install as an App (PWA)

WalletSOP is installable as a Progressive Web App. Once installed, it runs from a home-screen or desktop icon and works fully offline — no browser tab, no internet connection, no Node.js, no command line required.

This is functionally equivalent to the "Run Fully Offline" instructions above, without needing to clone the repo or run a build yourself.

What gets cached: only the app's own static files — index.html, the bundled JS/CSS, manifest.json, favicon.svg. Caching happens locally in your browser after your first visit; a cache-first strategy serves these offline afterward.

What does not get cached, stored, or sent anywhere: anything about you, your answers, or your audit. The service worker performs no background sync, push notifications, telemetry, or analytics, and never makes a "check for updates" network call. It only handles same-origin requests for the app's own files — any cross-origin request passes through untouched.

The service worker (public/sw.js) and manifest (public/manifest.json) are part of the same auditable public source as everything else in this repository — read them yourself before installing.

Build Checksum

Each tagged release publishes a SHA-256 checksum of its build output, computed by CI at release time — never hand-written. Find the checksum for a given version in that release's GitHub Release notes, or recompute it yourself:

bash
npm install
npm run build
sha256sum dist/index.html dist/assets/*.js dist/assets/*.css

Compare the output against the checksum listed on the corresponding GitHub Release page. A mismatch means you are not running the code from that release — inspect the source before trusting it.

Rubric Version

Current rubric: v1.0 (2026-08-05)

The scoring rubric is versioned and dated in a single canonical location (src/lib/diagnostics.js), imported everywhere it's displayed, so the in-app rubric view and the footer can never show mismatched versions. Any future change to scoring weights will be tracked below and the version number will increment.

Changelog
v1.0 (2026-08-05) — Initial rubric. Four independent sub-scores (Entropy, Backup & Storage, Access Control, Operational Hygiene). Fatal exposure path in Backup & Storage.
Tech Stack
React 18
Vite
Tailwind CSS
lucide-react
Project Structure
src/
├── App.jsx                    Routing
├── lib/
│   ├── diagnostics.js          Scoring engine — canonical rubric source
│   └── release-info.js         Build/release constants (checksum, commit)
├── pages/
│   ├── Home.jsx                 Audit flow
│   ├── EntropyCalculator.jsx    Standalone entropy calculator
│   └── BestPractices.jsx        Static reference content
└── components/walletsop/        UI components
public/
├── manifest.json                PWA manifest
├── sw.js                        Offline service worker (asset caching only)
└── favicon.svg
.github/workflows/
└── release-checksum.yml         CI: builds + publishes real checksums on tag push
Contributing

This is a public-interest security education tool — issues, corrections to the rubric's underlying assumptions, and clearer explanations are all welcome. If you're proposing a change to the scoring rubric itself, please explain the specific best-practice source backing the change; this project's credibility depends on every scoring rule being traceable to something documented, not just assumed.

Support

If this project is useful to you, consider supporting continued development:

GitHub Sponsors: (link)
Ko-fi: (link)

Support links are entirely optional and separate from the tool itself — nothing about the audit, its scoring, or its recommendations is affected by whether you use them.

License

MIT — see LICENSE.
