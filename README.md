# CyberSpartans — The Awakening

A four-second browser animation using the approved Awakening visual, with a metal reveal, digital assembly wipe, moving cyan particles, visor pulse and wordmark fade.

The artwork is a static key frame animated using CSS and canvas; this is not a rendered 3D video.

## Behaviour

- Normal URL: plays the intro and redirects to https://cyberspartans.co.uk/ after four seconds plus a 450ms fade.
- `?preview=1`: plays the intro, then offers Enter CyberSpartans and Replay intro.
- Skip intro immediately completes preview mode or opens the homepage in normal mode.
- Repeat visits in the same browser session bypass the intro in normal mode.
- Reduced-motion visitors see the final frame in preview mode and proceed directly to the homepage in normal mode.
- No sound or third-party libraries.

## Integration with the existing website

This project is a separate preview and has not changed cyberspartans.co.uk. To integrate the effect, copy the asset and stage styles/scripts into the existing homepage as an overlay. Replace the redirect in `enter()` with overlay removal so the homepage is revealed in place. Preserve the session and reduced-motion checks. Include the homepage content beneath the overlay and keep it available if JavaScript fails.
