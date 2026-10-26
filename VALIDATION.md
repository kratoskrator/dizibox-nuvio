# Verification — 26 October 2026

## Changes in 1.0.1

- Start the independent metadata and series-directory requests together.
- Stop checking additional media candidates once one source is verified.
- Continue to an independent listed player after another host is blocked; a blocked origin is not retried during that lookup.
- Share a 45-second budget across the lookup, cancel outstanding requests when it ends, and retry a transient GET failure only once.
- Count UTF-8 response bytes and reject a response at the native 1 MiB quota, including possible native truncation at exactly the quota.
- Never manually copy site cookies. The native redirect handler does not safely scope a supplied Cookie header to one origin.
- Validate HLS entries and unsupported DRM, derive quality from the manifest, and require the detail-page year to match exactly when available.

## Automated tests

42 tests passed in the development workspace. They cover exact title/year/season/episode matching, later-season links, safe player alternatives, static AES decoding, subtitles, malformed/DRM playlists, real manifest quality, frame cycles, native byte boundaries, transient retry limits, origin block caching, no manual cookies, cancellation without guest timers, settings and concurrent metadata/directory startup.

One optional captured-page test skips outside the development workspace. The remaining 41 tests are self-contained and travel in source.zip.

10 additional cases passed in the finished bundle under the official Nuvio TV QuickJS polyfill: two captured real series replays, movie rejection, missing metadata key, a 403 directory block, exact-quota ASCII and multibyte bodies, returning the first source while an unused candidate would hang, cancellation of a hanging directory when metadata fails, and an independent player after a blocked primary. No native requests were left pending. The first-source case did not fetch its unused candidate.

The test guest used 32 MiB memory and a 512 KiB stack. These are deliberately strict test limits; they do not imply every current TV app uses these exact quotas. CryptoJS was supplied from the official 4.2.0 distribution, as in the Nuvio runtime. Downloaded website scripts were never executed.

## Finished-bundle live checks

| Case | Result | Time including playlist checks |
| --- | --- | --- |
| Breaking Bad S2E3 | One 1080p HLS source; exact listed later-season episode; 7 website/player requests HTTP 200 | 4.97 seconds |
| Squid Game S1E1 | One 1080p HLS source; 6 website/player requests HTTP 200 | 3.79 seconds |

These runs executed the finished bundle in QuickJS using the TV's exact guest polyfill and its unmodified native HTTP service, fetched from NuvioMedia/NuvioTVSmart on 26 October 2026. TMDB metadata was a local fixture; all directory, detail, season, episode, wrapper, encrypted player and manifest requests were live anonymous requests. No real API key or login was borrowed.

Returned master and complete media playlists responded with HTTP 200 using the plugin's playback headers. Media playlists contained 1,502 and 1,193 segment entries. No video segments or complete video files were downloaded. All provider requests omitted Cookie and Authorization headers; metadata credentials are restricted to the metadata API.

The directory was 982,553 UTF-8 bytes and contained 4,834 parsed series entries. It remains inside the TV's 1 MiB limit with 66,023 bytes remaining. Further directory growth can require a new lookup strategy.

Final bundle: 43,084 bytes, SHA-256 `b616b1ae2c2f85d4a78cfce2a7203f648f723cb5003367ba7a3e1114058ec658`.

Official worker SHA-256: `7a8487fda3de07d4375d19bc9267067aeb18809207968fe3cb758b8253f019ae`.

Official network service SHA-256: `46e162bf569633ea1575eba9530630bc636c6f46a067bbb17e6636ab055fa743`.

## Scope and limits

Physical Samsung TV playback remains untested. These checks establish source extraction, guest JavaScript compatibility and playlist availability from the tested network; TV decoding, network restrictions and website availability can differ.

The provider still uses the public directory rather than the site's slow search endpoints. Tested players use the DBX Pro/Molystream path; arbitrary third-party formats are not guaranteed. Browser verification and access restrictions are reported and are not solved by this provider. This is a series provider and intentionally returns no movie sources.
