# Football VAR Pro — Stage 1 (Foundation)

Native Android app (Kotlin, Jetpack Compose, Material 3, Navigation Compose, Room, DataStore).
Package: `com.footballvarpro.app` · minSdk 26 · targetSdk/compileSdk 34.

## Open in Android Studio
1. Unzip `FootballVARPro-Stage1.zip`.
2. Android Studio (Koala 2024.1+ recommended) → **File ▸ Open** → select the `FootballVARPro` folder.
3. Let Gradle sync finish (internet needed once; Studio downloads Gradle 8.7 and dependencies). Use JDK 17 (bundled JBR is fine).
4. If Studio asks for a missing Android SDK 34, accept the install prompt.

> The Gradle wrapper *JAR* is not included. Android Studio uses `gradle/wrapper/gradle-wrapper.properties`
> automatically. For command-line builds, run `gradle wrapper --gradle-version 8.7` once, then use `./gradlew`.

## Build the APK
- Studio: **Build ▸ Build Bundle(s) / APK(s) ▸ Build APK(s)** → `app/build/outputs/apk/debug/app-debug.apk`
- CLI: `./gradlew assembleDebug`

## Install on a phone
1. Phone: Settings ▸ About ▸ tap *Build number* 7× ▸ enable **Developer options ▸ USB debugging**.
2. Connect via USB, accept the prompt, press **Run ▶** in Android Studio, **or**
   `adb install -r app/build/outputs/apk/debug/app-debug.apk`
3. Or copy the APK to the phone and open it (allow "Install unknown apps").

## Project structure
```
app/src/main/java/com/footballvarpro/app/
  MainActivity.kt, FootballVarApp.kt, AppContainer.kt
  model/        AppMode, Match (+status/type/draft), MatchClock
  database/     AppDatabase, entity/ (Match, Camera, Recording, MatchEvent, Highlight), dao/
  repository/   MatchRepository, CameraRepository, mappers
  settings/     SettingsRepository (DataStore: remembered mode, camera ID)
  storage/      StorageMonitor (real free/total space)
  camera/       DeviceStatusMonitor (real battery + network state)
  recording/    RecordingState (always IDLE in Stage 1)
  viewmodel/    Root, ModeSelection, Admin, CameraMode, Match, Settings, Factory
  navigation/   Routes, AppNavHost
  ui/           theme, components, mode, admin, camera, match, settings,
                gallery, events, replay, varreview, highlights
```

## What is implemented
- Dark broadcast-style theme; mode selection (Admin / Camera) remembered locally; "Change mode" in both mode screens and Settings.
- Admin dashboard: 2×2 camera placeholders (NO SIGNAL, inactive LIVE badges), recording status, connected cameras (from DB, 0), real match clock, real storage readout, all 8 requested buttons.
- Camera Mode: camera ID (saved), preview placeholder, connection status, real battery %, real network type, target resolution/bitrate labels, CONNECT / DISCONNECT with a Stage 2 notice.
- Match Management: all fields, validation, Room persistence, status flow CREATED → first half → half-time → second half → finished.
- Settings: all 9 sections; unimplemented items are marked "LATER STAGE".
- Navigation to every screen; later-stage screens show a "Not implemented yet" state.
- Room database v1 with Match, Camera, Recording, MatchEvent, Highlight tables and DAOs.
- Permissions: only `ACCESS_NETWORK_STATE`.

## What to test
1. First launch shows mode selection; pick a mode, kill the app, relaunch → it opens in that mode.
2. "Change mode" (top bar and Settings) returns to mode selection; the last mode shows a LAST USED tag.
3. Admin: START/STOP RECORDING show "Not implemented yet"; REPLAY / VAR / HIGHLIGHTS / EVENTS / GALLERY open placeholders; SETTINGS and MATCH SETUP open real screens.
4. Match Management: validation errors; create a match; start → half-time → second half → end. The Admin clock follows. Rotate the device mid-match.
5. Camera Mode: toggle Wi-Fi/airplane mode (network tile updates), plug in the charger (battery tile), change camera ID and relaunch.
6. Rotate phones/tablets: Admin switches to a two-column layout on wide screens.

## NOT implemented (later stages)
WebRTC, camera discovery, Wi-Fi streaming, live video, camera preview/capture, recording, replay buffer, VAR processing, highlight generation, event logging UI, gallery, automatic storage cleanup.
