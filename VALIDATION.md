# Verification — 16 October 2026

## Automated tests

32 tests passed in the development workspace. They cover directory matching, remake years, non-Latin title handling, exact episode selection, later-season pagination, alternative player pages, static AES decoding, subtitles, invalid playlists, frame cycles, request limits, verification blocks, settings and cookie scoping. One captured-page test skips outside the development workspace; the remaining synthetic tests are self-contained.

The final bundled provider is 34,750 bytes. It ran under Nuvio's current TV QuickJS polyfill with the official CryptoJS 4.2.0 UMD source, a 32 MiB memory limit and a 512 KiB stack limit. Captured real Breaking Bad and Squid Game pages returned verified streams. Movie requests performed no network activity, and blocked requests returned no sources with a sanitized warning.

## Finished-bundle live checks

| Case | Result | Lookup time |
| --- | --- | --- |
| Breaking Bad S1E1 | One verified HLS stream; all six real lookup requests HTTP 200 | About 5.4 seconds |
| Squid Game S1E1 | One verified HLS stream; all six real lookup requests HTTP 200 | About 5.3 seconds |

These runs executed the finished bundle in QuickJS with the TV polyfill and official CryptoJS. TMDB metadata was supplied as a local fixture; all directory, series, episode, wrapper, encrypted player and HLS requests used real native HTTPS. No borrowed API key was used.

Returned master and media playlists were checked again with the plugin's playback headers. Both returned HTTP 200; media playlists contained 1,885 and 1,193 segment entries respectively. Video segments were not downloaded.

The largest live body was the series directory at 981,585 UTF-8 bytes, inside the TV's exact 1 MiB response limit with approximately 67 KiB remaining. The Squid Game series and episode pages also fit this limit.

## Scope and limits

This verifies current source extraction, TV JavaScript compatibility and playlist availability from the tested network. Actual decoding/playback on a physical Samsung TV has not been tested. Site availability, codecs, redirects, protection rules and response sizes may change.

The site's AJAX and regular search routes did not respond within the test timeout. The finished provider uses the public directory and does not issue those search requests. Current tested sources use the DBX Pro/Molystream path; arbitrary third-party player formats are not guaranteed.
