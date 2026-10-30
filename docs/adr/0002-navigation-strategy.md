# ADR 2: Navigation Strategy

| **Status**     | Accepted                                                                           |
|----------------|------------------------------------------------------------------------------------|
| **Date**       | Oct 30, 2026                                                                       |
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

The YouTube player will live in the app’s root layout instead of inside a tab screen. When the app opens and no song is loaded, there is no player on screen, so the user has the full screen for browsing. When the user selects a song, the app opens the full-screen Now Playing view and the player plays at full size. If the user leaves Now Playing to browse while the song continues, the same player shrinks to a pinned position at the top of the screen, at full width and at least YouTube’s 200 by 200 pixel minimum, with our own play/pause and next controls beneath it, and the tab screens appear below it. Tapping the pinned player returns to the full-screen Now Playing view. The player is resized, never recreated, so playback is not interrupted when switching between the two. The current song, queue, and signed-in user will be stored in shared state (React Context).

## Consequences

**Playback continues across navigation:** Music keeps playing while the user moves between tabs, because the player lives in the root layout. If it were placed inside a screen, it would be removed when the user changed tabs and the song would stop.

**Less room for content while playing:** While a song is playing and the user is browsing, the pinned player permanently takes about a quarter of the screen, which leaves less space for content. We accept this to follow YouTube’s player requirements. When no song is playing, the full screen is available.

**More shared state:** The current song, queue, and user are shared by every screen, so we need to manage that state carefully.

**Room to grow:** Adding screens later is easy, as long as the tab bar stays at five items or fewer.

**Player must survive resizing:** The design depends on the same player staying mounted while it changes size between Now Playing and the pinned position. If the library restarts the song when its container is resized, we will fall back to keeping the player at a fixed size at the top of the screen. We will test this early on a real Android phone.
