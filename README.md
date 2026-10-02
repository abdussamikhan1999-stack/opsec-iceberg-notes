# OPSEC Iceberg Notes

Notes distilled from ["The OPSEC Iceberg Explained"](https://www.youtube.com/watch?v=JQiqTk9Mkxs)
(YouTube, creator `dzuma`) — an "iceberg meme" walkthrough of operational-security
and privacy practices, tiered from mainstream to extreme. The video mixes real,
actionable tooling/technique with internet-culture jokes; these notes keep the
former and drop the latter.

Each tier gets stricter about what it assumes: lower tiers trust most
mainstream services and just pick the privacy-respecting option; higher tiers
assume the service itself, the network, or the hardware is the adversary.

## Tier 1 — Nothing to Hide, Nothing to Fear

Baseline hygiene. The "nothing to hide" argument assumes today's laws/regimes
stay fixed — data collected under current legality can be retroactively
weaponized if either changes.

- **[privacyguides.org](https://www.privacyguides.org)** — curated directory of
  privacy-respecting software, sorted by threat level.
- **WSL/VMs are not a Linux switch** — the Windows host still controls the
  hypervisor, networking, and telemetry underneath.
- **ProtonMail** — Swiss, zero-access encrypted email, no phone number
  required; encryption only holds inside the Proton ecosystem unless you
  import PGP keys. Complied with a Swiss court order that exposed a French
  climate activist's IP (2021) — don't centralize VPN + mail + storage with
  one provider.
- **Tuta Mail** — German, open-source, E2E, no JS-client requirement. Complied
  with a 2020 German order to intercept one user's future unencrypted mail.
- **VPNs only hide traffic from your ISP**, not full anonymity. Pick
  battle-tested no-log providers: Mullvad (RAM-only logs, survived a 2023
  police raid with nothing to hand over) or Proton VPN (458 Swiss legal
  demands, no logs existed to give up).
- **DuckDuckGo** — aggregates Bing/Brave without building an ad profile, still
  a third-party trust dependency. Its *browser* (not search) was contractually
  forced to allow Microsoft tracking scripts in 2022.
- **Signal vs. Telegram** — both require a phone number. Telegram group chats
  are unencrypted cloud chats by default and Telegram discloses IP/contacts/
  metadata/messages on valid legal orders; Signal discloses only account
  creation time and online timestamps.
- **Phishing literacy** — lookalike domains (`paypal-account-verify.com` vs.
  `paypal.com`) are the actual common failure mode, not exotic exploits.
- **Password managers** over memorized/reused passwords.
- **Open source vs. security-by-obscurity** — closed source hides
  vulnerabilities, doesn't remove them.
- **Antivirus** — Windows Defender + common sense covers most people; ClamAV
  on Linux if you want something.
- **BitLocker defaults to uploading your recovery key to your Microsoft
  account**, and Microsoft confirmed handing BitLocker keys to law
  enforcement under warrant. Use **VeraCrypt** (Windows) or **LUKS** (Linux)
  instead.
- **Closed-source "privacy" tools have burned people before**: NSA paid RSA
  $10M to backdoor Dual_EC_DRBG; the FBI secretly built and ran the "Anom"
  encrypted-phone company; Zoom marketed true E2E during COVID while it was
  actually transport-only encryption.

## Tier 2 — Chan Anon

Where "privacy" starts meaning the service/network is assumed adversarial, not
just annoying.

- **Shadowsocks** — disguises VPN traffic as generic encrypted noise to beat
  DPI/VPN blocking; built into Mullvad, and as Proton VPN's "Stealth" mode.
- **FSF-endorsed distros** — fully "libre" (no proprietary firmware blobs);
  notably Arch, Gentoo, and even Tails *don't* qualify — PureOS and Parabola
  do.
- **Snowden/PRISM (2013)** — the leak that kicked off the post-2013
  encryption-by-default era; confirmed NSA mass surveillance with
  Microsoft/Google/Facebook/Apple/Skype cooperation.
- **VeraCrypt** — hidden volumes with plausible deniability (random-noise
  header, unlike LUKS); supports hardware key files.
- **qBittorrent over uTorrent** — open source, no telemetry, has a kill
  switch; uTorrent bundled a hidden cryptominer in 2015.
- **Privacy frontends** (Invidious/Piped, Nitter, Teddit/Libreddit, etc.)
  shift trust from the platform to the frontend operator — self-host your own
  when you can.
- **The fingerprinting paradox** — heavy browser customization (extensions,
  tweaks) makes you *more* unique, not less. Tor Browser sidesteps this by
  making every user's fingerprint identical.
- **Tor Browser** — three-hop relay, no single node sees both your IP and
  destination. Real residual risks: a government controlling both entry and
  exit nodes, future decryption of archived traffic, global passive
  timing-correlation, and website fingerprinting via traffic patterns. Use it
  from public/non-home networks, never reuse usernames across identities.
- **PGP weaknesses** — static keys mean a stolen key retroactively decrypts
  years of history; metadata (subject/sender/recipient/timestamp) stays in
  the clear; relies on RSA/ECC, both vulnerable to future quantum attacks.
- **De-Googling** — don't put all your eggs in one ecosystem's basket,
  including Google's *or* Proton's.
- **GrapheneOS over LineageOS** — LineageOS needs an unlocked bootloader,
  which undermines Android Verified Boot.
- **Bitcoin is pseudonymous at best** — exchanges do blacklist "tainted"
  coins. Mixers/CoinJoins or a Bitcoin→Monero→Bitcoin swap defeat
  common-input-ownership tracing heuristics; avoid KYC exchanges generally.
- **OPSEC vs. PERSEC** — OPSEC is 1966 US military doctrine (Vietnam-era
  Operation Purple Dragon); the civilian "privacy = OPSEC" usage is really
  closer to PERSEC (personal security).

## Tier 3 — Enlightened Glowy

Self-hosting and hardened-OS territory.

- **Self-hosted Matrix**, federation disabled, optionally as a Tor hidden
  service — a private island instead of a federated network (your domain
  name can still deanonymize you).
- **[digdeeper.club](https://digdeeper.club)** — community-maintained privacy
  software directory, mirrored on Tor/I2P.
- **SearXNG** — self-hosted meta-search proxy. Self-hosted VPS instances log
  full plaintext queries via Nginx by default — switch GET to POST to stop
  that, and route the instance itself through a VPN/Tor.
- **Ollama + Open WebUI + SearXNG** — fully local LLM assistant, no cloud API;
  watch for prompts/outputs lingering in shell history/logs.
- **Monero (XMR)** — ring signatures + stealth addresses + encrypted amounts;
  using it at all is itself a flaggable signal per leaked XKeyscore rules.
  Run your own full local node — many public nodes are run by government or
  chain-analysis firms. Vulnerable to Sybil attacks against its Dandelion++
  routing; mitigated with constant network padding or a mixnet like Nym.
- **Avoiding systemd** (Runit, OpenRC, s6, sysvinit) — journald's append-only
  binary logs make selectively scrubbing one entry nearly impossible without
  leaving forensic evidence.
- **Whonix** — two-VM Tor setup (Gateway routes everything through Tor,
  Workstation has no direct route out). Vulnerable to TCP-timestamp
  "clock-skew" hardware fingerprinting if the physical machine is seized.
- **Tails** — live amnesic USB OS, RAM wiped within seconds of unplugging
  (mitigates cold-boot attacks on *system* RAM, not GPU VRAM).
- **Tailscale** (WireGuard mesh) paired with self-hosted services to avoid
  port-forwarding exposure.
- **No-JS fingerprinting is severe** — JS can read CPU cores, RAM, GPU model,
  battery %, screen size, connected devices, storage size, timezone, and
  keystroke/mouse timing. The 2013 FBI "Freedom Hosting" case used a
  Firefox/Tor 0-day exploit this way to reveal visitors' MAC
  addresses/hostnames.
- **GrapheneOS specifics** — auto-reboots every 18h (configurable) back to a
  harder-to-extract "before first unlock" state; duress PIN wipes the device
  (legally risky if triggered during a lawful seizure); blocks new USB-C
  connections until unlocked (defeats tools like Cellebrite).
- **Libreboot + me_cleaner** on older ThinkPads strips most of the Intel
  Management Engine (can't be fully removed on modern CPUs); self-signed boot
  verification defends against evil-maid attacks.
- **Intel ME / AMD PSP** — closed-source co-processors at "ring -3," full
  memory/key access even when the machine is powered off.
- **OpenBSD** — security-first Unix lineage (OpenSSH, OpenNTPD, LibreSSL came
  from it); its PF firewall syntax is far more readable than
  iptables/nftables; can be run as a two-NIC Tor gateway box to force a whole
  network through Tor at the network level.
- **Compartmentalized identities** — separate email/username/password per
  account/persona via a password manager (e.g. KeePassXC).
- **Pi-hole over NextDNS** — Pi-hole is self-hosted and keeps DNS logs local;
  NextDNS is cloud-hosted and trust-dependent.
- **Self-hosted cloud avoids third-party legal-disclosure regimes** (US Stored
  Communications Act, EU e-evidence orders, UK Investigatory Powers Act,
  proposed EU "Chat Control").
- **age + minisign** — modern PGP replacement (X25519 + ChaCha20-Poly1305),
  smaller keys, scriptable, no metadata leakage — good for automated
  encrypted backups.
- **I2P** — fully decentralized "garlic routing," separate inbound/outbound
  tunnels per peer; every user also routes others' traffic, which is itself
  cover noise against passive timing-correlation attacks.

## Tier 4 — Ascended Skitso

- **PRNU ("sensor noise fingerprinting")** — every camera sensor leaves a
  unique noise pattern on every photo that survives EXIF stripping; mitigate
  with cropping + non-linear resizing, or PRNU/BM3D denoising, or just use a
  burner camera for sensitive shots.
- **Forget** — lightweight PHP privacy search proxy, avoids SearXNG's JS
  requirement and captcha issues; proxies media server-side.
- **Qubes OS** — every app/process in its own Xen VM ("qube"); network/USB
  hardware isolated; ships with Whonix built in. Trade-off: no GPU
  acceleration under Xen.
- **Live-USB-only computing** lets you use any public machine; still
  vulnerable to evil-maid USB-swap attacks and residual RAM data after
  shutdown.
- **VLESS + Reality** — VPN obfuscation that mimics a real TLS handshake,
  near-undetectable by DPI; add XTLS Vision to avoid the "TLS-in-TLS"
  statistical tell. Use a non-mainstream VPS provider, pay with Monero, keep
  per-proxy user counts low.
- **Hysteria 2** — QUIC-based, mimics HTTP/3 traffic, holds speed on
  throttled/lossy networks; add Salamander obfuscation if QUIC itself is
  blocked.
- **XMPP + OMEMO + Tor** — federated, no phone number, Signal-style
  double-ratchet E2E, network location hidden via Tor/onion service. Still
  leaks some metadata (room names, delivery receipts, push notifications)
  depending on setup.
- **Physically separate Whonix Gateway/Workstation** onto two machines
  instead of two VMs on one host, to isolate a hypervisor-level compromise.

## Tier 5 — Psychosis and Point of No Return

Alternative-network territory — mostly impractical for daily use, useful to
know exists.

- **Yggdrasil** (successor to CJDNS) — self-healing mesh via greedy routing,
  IPv6 address cryptographically derived from your own keypair; still
  vulnerable to bootstrap-peer poisoning. Hide your home IP behind it with
  Hysteria 2.
- **Reticulum** — cryptographic networking stack over Wi-Fi, Ethernet, serial,
  or LoRa radio; 256-bit destination keys instead of IP; per-session keys are
  destroyed after each connection. Raw radio interface identity isn't
  encrypted by default (vulnerable to direction-finding unless IFAC
  encryption is configured); very low bandwidth.
- **RNode / LoRa hardware** — turns a cheap LoRa board into a Reticulum
  transceiver for off-grid, carrier-free comms over kilometers — still
  jammable/direction-findable.
- **DN42 / CRXN** — community "alternative internet" overlays using real
  BGP/Babel peering; mainly useful as hands-on networking education.
- **Sneakernet** — physically carrying encrypted data between parties, immune
  to digital interception; the risk shifts to physical seizure.

## Tier 6 — Primordial Compiler

The logical endpoint, mostly conceptual: total off-grid self-sufficiency
(growing your own food, generating your own power/water) removes every
third-party dependency and therefore every point of data collection. Framed
in the video via Ted Kaczynski references — the actionable kernel is just
"self-host everything, including your physical survival," everything past
that is the video's joke material.

---

*Jokes/memes dropped from these notes on purpose (iceberg-meme flavor, no
real technique): Nazim-from-Skyrim and "chad pentester" bits, 4chan raid
history, Lambda+.js, "I made my own compiler" Terry Davis worship, PGP-by-
pigeon, "owns chickens that compile FOSS eggs," hikikomori jokes, "nearest
human is 1 hour away," and the rest of Tier 6's absurdist escalation.*
