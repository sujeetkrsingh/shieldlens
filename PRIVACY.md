# ShieldLens privacy policy

_Last updated: 23 October 2026_

ShieldLens is a browser extension that checks the security of the website you are viewing. It is designed to work entirely on your device.

## The short version

- Nothing from the page you scan is ever uploaded. Its content, code, cookies, form data and your scan results are never sent anywhere.
- ShieldLens has no servers, accounts, analytics or telemetry. It does not track the websites you visit.
- All analysis runs on your device, including the optional AI explanations.
- ShieldLens only downloads from the site you scan, and from the servers hosting that page's scripts (which can be turned off).
- Optional domain and certificate checks, both off by default, send only domain names, to a DNS resolver and to the public Certificate Transparency search crt.sh.

## Data processed on your device

When you scan a page, ShieldLens reads these and analyses them in memory:
- the page's response headers
- cookie attributes (names and flags, not values)
- page structure
- scripts and browser storage (Web Storage, IndexedDB, Cache Storage, service workers)

For session cookies, ShieldLens keeps a short one-way hash of the value. That is enough to tell whether a session ID changed during a recorded login or logout, and useless for recovering the ID.

With live monitoring on for a site, ShieldLens also watches what the page does in your browser, including:
- scripts that read password or card fields
- data sent to other sites
- the site's own API responses

Card numbers, passwords and API data are checked inside the page and never reported or stored. Only the kind of data, the endpoint and the script responsible are kept.

- **Secrets and personal data.** Anything found in page code or storage, such as API keys or card numbers, is never stored or shown in full. Only its type and a masked preview are kept.
- **On-device AI explanations and chat.** If you click "Explain & fix with on-device AI", the finding is given to Chrome's built-in model (Gemini Nano), which runs on your device. That means the finding's title, description, masked evidence and suggested fix. Follow-up questions you type, and the model's answers, are handled the same way. Neither ShieldLens nor Chrome sends any of it anywhere, and the conversation is not saved: it is gone when you close the panel or click "Start over". Chrome downloads the model from Google once, the first time it is used.
- **Page screenshot.** When ShieldLens can capture the visible tab (you opened it from its icon, shortcut or menu), it saves a small screenshot with the scan for the HTML/PDF report. It stays on your device; turn it off in Settings if your pages show personal data.
- **What is saved.** These are saved in your browser profile (`chrome.storage.local`), never synced:
  - scan results and history
  - pages you chose to monitor for changes, with their baseline (script addresses and content hashes, security headers)
  - script baselines and triage decisions
  - settings
  - a log of the extension's own requests

  You can delete them at any time in Settings → Data.

## Network requests ShieldLens makes

ShieldLens's own requests are read-only downloads. That means GET requests, plus one OPTIONS request in owner-verified checks. None of them carries data from the page, and none sends a referrer.

This is enforced in the code: a request that would send data, or go anywhere not listed below, is refused before it leaves the browser. Automated tests check this on every build. Every request is listed in Settings → Scanner activity.

1. **The website you scan.** These requests are the same kind your browser sends when you open the page, and they carry your cookies for that site:
   - a re-request of the page
   - the script files it loaded
   - with domain checks on, `/.well-known/security.txt`
   - only after you prove you own the site, a few ordinary read-only requests: other pages, a missing page, an OPTIONS request, a fixed list of well-known file paths (such as `/.env`), asset folders, a GraphQL schema query, robots.txt, and the site's API addresses
   - for owner-verified checks, the API requests come from a sandboxed ShieldLens page. That page has no access to ShieldLens itself, and it tests whether any website could read the API.
   - for pages you choose to monitor for changes: the page and its scripts, re-downloaded on the schedule you pick (daily or weekly), in the background

   If the site redirects one of these requests to a different site, ShieldLens ignores the response.
2. **Servers hosting the page's own scripts.** This is on by default, and you can turn it off in Settings → "Check scripts hosted on other sites". Pages often load scripts from other servers, such as a CDN. ShieldLens downloads those same public files again to check them for known-vulnerable libraries.
   - They are fetched without cookies and without a referrer.
   - The only information in the request is the file's address, which the page itself published.

   If such a download fails, ShieldLens makes one more request to the same address to tell an unreachable host from one that refuses to share the file.

   With this setting off, ShieldLens contacts only the site you are scanning.
3. **Cloudflare's public DNS resolver (`cloudflare-dns.com`).** This happens only if you turn on domain checks or certificate checks, which are both off by default. ShieldLens sends only domain names:
   - the scanned domain, to look up its SPF, DMARC, CAA, MTA-STS and DNSSEC records
   - with certificate checks on, its subdomains, to look for takeover risks
   - the host name of a script the page loads that couldn't be reached, to see whether the domain still exists

   Cloudflare's [resolver privacy policy](https://developers.cloudflare.com/1.1.1.1/privacy/public-dns-resolver/) applies to those lookups.
4. **crt.sh, the public Certificate Transparency search.** This happens only if you turn on certificate checks, which are off by default. ShieldLens sends the scanned domain name, and nothing else, to list its certificates and subdomains.

As with any download, the servers above can see your IP address. No other server is ever contacted. The extension's own pages are locked so they cannot load images, fonts, styles or scripts from the internet. Exported HTML reports are locked the same way.

## Permissions

- ShieldLens installs with no permission warnings.
- The `alarms` permission is only used to re-check pages you chose to monitor for changes, on the schedule you pick.
- Site access is requested one site at a time, when you scan it.
- Optional permissions (tab access, notifications) are requested only when you switch on the feature that needs them, and can be revoked at any time.

## Changes to this policy

If this policy changes, the new version is published at the same address with a new "Last updated" date.

## Contact

Questions or concerns: open an issue in the GitHub repository where this policy is published, or use the developer contact on ShieldLens's Chrome Web Store page.
