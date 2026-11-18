# ADR 2: Navigation Strategy

| **Status**     | Accepted                                                                           |
|----------------|------------------------------------------------------------------------------------|
| **Date**       | Oct 30, 2026 (Updated Nov 17, 2026)                                                                      |
| **Decided by** | Jonah Rideout, Ahmed Yassine Messaoudi, Simon Waites, Guilherme Gatti, Jon Ibarlin |

## Context

Users of Mulo need to move quickly between browsing, searching, community content, and their saved playlists while a song keeps playing. The app has several main sections, and it should feel like a streaming app similar to Netflix and Spotify. Our mobile course uses Expo Router for navigation, and we have used it in our assignments. Songs play through the YouTube IFrame Player API, and YouTube requires embedded players to stay visible and be at least 200 by 200 pixels, so our navigation design has to leave room for the player.

## Options Considered

| **Option**                                            | **Pros**                                                                                                                                                                   | **Cons**                                                                                                                                            |
|-------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------|
| Expo Router bottom tabs with a pinned player (chosen) | Taught in our course. Main sections are always one tap away. The player is visible in a predictable place whenever a song is loaded, which follows YouTube’s requirements. | Limited to about 3 to 5 tabs. The player takes a permanent share of the screen while a song is loaded.                                              |
| Expo Router bottom tabs with a floating player card   | The player can be moved out of the way of content. Leaves the top of the screen free.                                                                                      | More code for dragging and overlays. The card can cover content and is harder to keep clearly visible, which risks breaking YouTube’s requirements. |
| Drawer navigation                                     | Can hold many menu items.                                                                                                                                                  | Hides the main sections and takes an extra tap to switch screens.                                                                                   |
| Stack navigation only                                 | Simplest to set up.                                                                                                                                                        | No quick way to move between main sections, and users would have to back out of screens to switch.                                                  |

## Decision

We will use Expo Router with a bottom tab bar of four tabs: Home, Search, Community, and Library. Account and settings will be reached from a profile icon, so the tab bar stays at four items.

The YouTube player will live in the app’s root layout instead of inside a tab screen. When the app opens and no song is loaded, there is no player on screen, so the user has the full screen for browsing. When the user selects a song, the app opens the full-screen Now Playing view, with the player at the top of the screen at full width and our own controls beneath it. If the user leaves Now Playing to browse while the song continues, the same player stays at the top of the screen at the same full width, which is above YouTube’s 200 by 200 pixel minimum, with our own play/pause and next controls directly beneath it, and the tab screens appear below it. Tapping the pinned player returns to the full-screen Now Playing view. The player keeps the same size in both views and is never recreated, so playback is not interrupted when switching between them. The current song, queue, and signed-in user will be stored in shared state (React Context).

## Consequences

**Playback continues across navigation:** Music keeps playing while the user moves between tabs, because the player lives in the root layout. If it were placed inside a screen, it would be removed when the user changed tabs and the song would stop.

**Less room for content while playing:** While a song is playing and the user is browsing, the pinned player permanently takes about a third of the screen, which leaves less space for content. We accept this to follow YouTube’s player requirements. When no song is playing, the full screen is available.

**More shared state:** The current song, queue, and user are shared by every screen, so we need to manage that state carefully.

**Room to grow:** Adding screens later is easy, as long as the tab bar stays at five items or fewer.

**Player must stay mounted:** The design depends on the same player staying mounted while its position changes between Now Playing and the pinned position. Because the player keeps the same size in both views, we no longer depend on the library handling a resize. If the song restarts when the player moves, we will fall back to showing the player in one fixed position in both views. We will test this early on a real Android phone.
