# ADR 4: Database Storage

| **Status**     | Accepted                                                                           |
|----------------|------------------------------------------------------------------------------------|
| **Date**       | Oct 30, 2026                                                                       |
| **Decided by** | Jonah Rideout, Ahmed Yassine Messaoudi, Simon Waites, Guilherme Gatti, Jon Ibarlin |

## Context

Mulo needs user accounts, saved playlists, user settings, and community features such as sharing songs. This data has to be shared between users and stored outside the device, so the app needs a remote database. A potential Jam feature (listening together) would also need updates to reach other devices quickly. We confirmed that we may use an external database and APIs.

Our program has covered relational databases, and at least one team member has used Supabase in a previous course project. We have one term to build the app, so we want a solution that does not require us to build and host our own server.

## Options Considered

| **Option**                   | **Pros**                                                                                                                                                                                                                                                    | **Cons**                                                                                                                                      |
|------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------|
| Supabase (chosen)            | Hosted PostgreSQL database, so it uses SQL and relational design like our course work. Built-in user authentication, so we do not store passwords ourselves. Realtime updates for features like Jam. Free tier. JavaScript library works with React Native. | Free tier has limits and may pause an inactive project. Security depends on setting up access rules correctly. Depends on an outside service. |
| Firebase                     | Popular, with authentication and realtime updates built in.                                                                                                                                                                                                 | Document database instead of relational, so it differs from what we learned. Harder to keep related data consistent.                          |
| Our own backend and database | Full control of features and hosting.                                                                                                                                                                                                                       | We would need to build, host, and secure a server. Too much extra work for our timeline.                                                      |
| Local storage only           | Simplest option. Works offline.                                                                                                                                                                                                                             | Cannot support accounts, sharing, or community features.                                                                                      |

## Decision

We will use Supabase as our remote database and authentication service. Accounts, playlists, user settings, shared songs, and community data will be stored in Supabase, and settings will be tied to the user’s account so they follow the user between devices.

We will use AsyncStorage only to keep the Supabase login session on the device, so users stay signed in when they reopen the app. Supabase gives us a relational database, user sign-in, and realtime updates without building a server, and it connects to our existing database knowledge because it is built on PostgreSQL.

## Consequences

**No server to build:** We can build accounts, shared playlists, community posts, and later a Jam feature without writing or hosting server code.

**Internet required for account features:** Sign-in, playlists, and community features need a connection. When the device is offline, the app will tell the user.

**Security must be set up correctly:** The Supabase keys are stored inside the app, so we must turn on row-level security so users can only read and change data they are allowed to. Our YouTube API key will also be inside the app, so we will restrict it to our app in the Google Cloud console.

**Limited YouTube quota:** The YouTube Data API has a daily quota, so we will reuse recent results instead of searching repeatedly, and we will check YouTube’s current rules on how long its data may be stored.

**Dependence on an outside service:** The free tier for Supabase has limits and may pause an inactive project, so we will check that our project is running before demos.

**Data tied to one provider:** Our data lives in Supabase, so moving to another service later would take work. We accept this for a course project.

**Login session stored unencrypted on the device:** AsyncStorage does not encrypt its data, so the Supabase login session is stored in plain form on the phone. We accept this for a course project because the app holds no payment or highly sensitive data, and encrypted storage such as Expo SecureStore could be added later.
