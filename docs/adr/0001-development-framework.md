# ADR 1: Development Framework

| **Status**     | Accepted                                                                           |
|----------------|------------------------------------------------------------------------------------|
| **Date**       | Oct 30, 2026                                                                       |
| **Decided by** | Jonah Rideout, Ahmed Yassine Messaoudi, Simon Waites, Guilherme Gatti, Jon Ibarlin |

## Context

We are building an Android music player app called Mulo. Users will be able to search and browse songs with the YouTube Data API and play them through the YouTube IFrame Player API, which will be embedded into our app. The app will connect to a remote database (Supabase) for accounts, playlists, and community features.

Our mobile course teaches Expo / React Native, and we have used it in our assignments, which are written in TypeScript. We have one term to design, build, and test the app. Two decisions were pre-set in the instructions: Android as the target device and Bootstrap as the CSS framework.

## Options Considered

| **Option**                     | **Pros**                                                                                                                                                                                                              | **Cons**                                                                                                                                                                                                                                                                                      |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Expo / React Native (chosen)   | We are most familiar with this setup, as it has been the focus of the course. It will use one TypeScript codebase for Android. The react-native-youtube-iframe library lets us run the YouTube player inside our app. | The YouTube player runs inside a WebView, so it is less smooth than native components. Background playback is not supported, because YouTube’s terms do not allow playing its content without the video visible. For the purpose of this project / course, this approach is still sufficient. |
| Ionic                          | Ready-made mobile UI components.                                                                                                                                                                                      | Has its own UI kit and tooling that are not taught in our course, and is most commonly used with Angular, which would require extra learning and research.                                                                                                                                    |
| Native Android (Kotlin / Java) | Best performance overall and full access to Android features.                                                                                                                                                         | This approach would take a much longer build time and wouldn’t be efficient for the nature of this project / course.                                                                                                                                                                          |

## Decision

We will build the app with Expo and React Native in TypeScript, targeting Android, and use the react-native-youtube-iframe library to play YouTube songs inside our own interface.

Bootstrap, the pre-set CSS framework, is a web-only library and does not work in React Native, so we are replacing it with React Native’s built-in StyleSheet and our own shared theme (colours, spacing, and reusable components). The instructions allow us to change the pre-set choices. Expo and React Native fit our scope because they are what our course teaches and what we have used in our assignments, so we can start building right away and spend our time on the player, playlists, and community features instead of learning a new framework.

## Consequences

**Dependency on a third-party library:** Song playback depends on the react-native-youtube-iframe library. If the library or YouTube’s embed changes, playback could break, so we will test it on a real Android phone early, before building the rest of the app around it.

**No background playback:** Music stops when the app goes to the background or the screen locks. We accept this limitation because YouTube’s terms do not allow playing its content without the video visible.

**Less smooth than native:** Because the YouTube player runs inside a WebView, it will feel slightly less smooth than native components. This is acceptable for the purposes of this course project.

**More custom styling:** Replacing Bootstrap means we write more of our own styles. In exchange, our design is fully our own, which fits our goal of a unique look.
