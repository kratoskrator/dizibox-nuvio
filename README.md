# DiziBOX TV for Nuvio

A series provider for Nuvio that runs directly on your device. It finds shows in Dizibox's public series directory, follows the actual season and episode links, resolves the current DBX/Molystream player, and verifies its HLS playlist before offering a source. No PC or hosted resolver is needed during use.

Version **1.0.1** starts metadata and directory requests together, returns the first verified source, and lets an independent listed player work when another host is blocked. Requests share a 45-second lookup budget, transient GET failures receive at most one retry, and native cancellation stops outstanding work after a lookup ends. It also enforces the TV's limit in UTF-8 bytes and avoids manually forwarding cookies through native redirects.

## Install on your TV

In Nuvio, open Settings → Plugins → Add repository and paste:

`https://raw.githubusercontent.com/kratoskrator/dizibox-nuvio/main/manifest.json`

Refresh and enable **DiziBOX TV**. Open a series, choose a season and episode, then select a DiziBOX TV source. This provider supplies series; keep a separate movie provider enabled for movies.

## Settings

- **Website address:** defaults to `https://www.dizibox.live`. Update this HTTPS origin if the site moves.
- **TMDB API key:** uses Nuvio's injected key when available. If your app does not supply one, enter your own TMDB v3 API key or API Read Access Token. No third-party key is embedded in the provider.

## How it works

The site's search endpoints timed out during testing, so this provider reads the public series directory instead. Matching uses the title, original title, available year and exact listed episode. Later seasons can require an additional season-page request. If the primary player has no usable source, the provider can try listed alternative player pages for that same episode.

Player encryption is decoded as static data using Nuvio's built-in `crypto-js` module. Downloaded JavaScript is never executed. The provider requires a Nuvio runtime with CryptoJS support, preserves playback request headers and includes subtitle tracks only when the player exposes them. HLS sources must contain actual playlist entries and must not declare unsupported DRM. Quality comes from the verified manifest's resolution; otherwise it is shown as Auto.

## Compatibility and validation

The bundle uses ES5 and Promise chains. It does not rely on Node built-in APIs, a browser DOM, Cheerio, `URL`, timers or a local server. It was tested under the current Nuvio TV QuickJS polyfill with official CryptoJS, 32 MiB memory and a 512 KiB stack.

On 26 October 2026, the finished bundle completed live checks for Breaking Bad S2E3 and Squid Game S1E1 in about 5.0 and 3.8 seconds, including verification of returned master and media playlists. Both returned 1080p HLS through the unmodified Nuvio TV network service. The checks used the TV's exact QuickJS polyfill with local TMDB metadata fixtures; website and playlist requests were live. See [VALIDATION.md](VALIDATION.md) for details.

Physical Samsung TV video playback has not been tested. Availability can change with site updates, network restrictions, TV codecs or runtime limits. The directory currently fits the TV's 1 MiB response limit; substantial growth could require a lookup update. Browser verification is reported as an access restriction and is not solved by this provider.

## Source, build and tests

The published repository includes the ready-to-install `manifest.json` and `dizibox.js`, plus complete source in `source.zip`. Extract the source archive, install Node.js 18 or newer, run `npm ci`, then `npm test`. The CryptoJS development dependency is for local tests; Nuvio supplies the runtime copy. `node build.js` rebuilds `dizibox.js` from `src/`.

Synthetic tests travel with the source. An optional test using local captured pages skips when those scratch fixtures are absent; no captured player tokens or website HTML are published.

## References and license

- [Nuvio provider documentation](https://github.com/yoruix/nuvio-providers) defines the manifest, settings and stream contract.
- [Maintained DiziBox CloudStream source](https://github.com/feroxx/Kekik-cloudstream/blob/master/DiziBox/src/main/kotlin/com/keyiflerolsun/DiziBox.kt) helped identify public season and player routes. This provider implements its own parsing and does not copy that GPL source.
- [CryptoJS](https://github.com/brix/crypto-js) provides the standard AES passphrase decoder through the Nuvio runtime.

Provider code is MIT licensed. The repository hosts code rather than video files. Use it with content you are authorized to access.
