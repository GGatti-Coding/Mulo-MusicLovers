# ADR 3: Hardware

| **Status**     | Accepted                                                                           |
|----------------|------------------------------------------------------------------------------------|
| **Date**       | Oct 30, 2026                                                                       |
| **Decided by** | Jonah Rideout, Ahmed Yassine Messaoudi, Simon Waites, Guilherme Gatti, Jon Ibarlin |

## Context

The main hardware Mulo depends on is audio output, which is produced by the YouTube player. The app also needs to know whether the device is online, because searching, signing in, and community features all need an internet connection. Each extra hardware feature adds packages, permissions, and testing time, so we only want the ones our features need. Early in planning we considered a song recognition feature that would use the microphone, but we removed it because it needed a third-party service that we could not rely on for the full term.

## Options Considered

| **Option**                                           | **Pros**                                                                                                                                                                                                                   | **Cons**                                                                                                                        |
|------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|
| Speaker / headphone audio and network check (chosen) | Covers every feature we plan. Needs only the internet permission, so users see no permission prompts. The network check lets us show a clear message when the user is offline. Expo provides a package for network status. | No lock-screen or notification controls.                                                                                        |
| Add the microphone for song recognition              | Would allow a Shazam-style feature.                                                                                                                                                                                        | Needs a runtime permission and a third-party recognition service with limited free usage. Outside what we can finish this term. |
| Add fingerprint / biometric login                    | Could protect user accounts.                                                                                                                                                                                               | Our app stores no sensitive data beyond a basic account, and it adds packages and testing.                                      |
| Add GPS / location                                   | Could suggest music based on location.                                                                                                                                                                                     | Not needed for our features. Adds permission and privacy concerns.                                                              |

## Decision

We will use the device speaker and headphone output through the YouTube player, and the Expo Network package to detect whether the device is online, so the app can show a message instead of failing when the connection is lost. We will not use the microphone, GPS, fingerprint, or camera.

Volume will be adjustable through our own in-app control, which sets the YouTube player’s volume, and through the phone’s physical volume buttons.

## Consequences

**Few permissions:** The app only needs the internet permission, so users are not asked for access to anything else, and there are fewer things to test.
**Clear offline behaviour:** Search, accounts, and community features need a connection, so when the device is offline the app tells the user instead of showing errors.

**No lock-screen controls:** Features like lock-screen and notification playback controls are not included, which also fits our decision not to support background playback. They could be added as future improvements.

**Volume may vary by device:** The in-app volume control depends on the YouTube player’s volume setting, which may behave differently across devices, so we will test it on a real Android phone. The phone’s physical volume buttons work as a fallback.

**Song recognition removed:** We are giving up a Shazam-style search feature in exchange for a smaller scope and fewer outside dependencies.
