# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Android library (AAR) providing a ringtone picker dialog. Fork of `com.kevalpatel2106:android-ringtone-picker`, currently at version 1.3.8-rf. Two modules: `ringtonepicker` (library) and `sample` (demo app).

## Build Commands

```bash
# Build debug
./gradlew assembleDebug

# Full build
./gradlew build

# Clean
./gradlew clean

# CI build (used by Travis CI)
./gradlew assembleDebug -PdisablePreDex --stacktrace

# Generate javadoc
./gradlew :ringtonepicker:javadoc
```

There are no tests in this repository.

## Architecture

The library lives in `ringtonepicker/src/main/java/com/kevalpatel/ringtonepicker/` with six classes:

- **RingtonePickerDialog** — `DialogFragment` with a Builder pattern. Entry point for consumers. Shows a loading spinner (ViewFlipper index 0) while ringtones load, then switches to a ListView (index 1). Handles sample playback via `RingTonePlayer`.
- **RingtonePickerDialog.Builder** — Fluent API configuring title, buttons, ringtone types (TYPE_RINGTONE, TYPE_NOTIFICATION, TYPE_ALARM, TYPE_MUSIC), default/silent options, sample playback, current URI, and listener. Call `.show()` to display.
- **RingtonePickerListener** — Serializable callback interface with `OnRingtoneSelected(String name, Uri uri)`.
- **RingtoneLoaderTask** — Package-private AsyncTask that loads ringtones in background, delegating to `RingtoneUtils` per type.
- **RingTonePlayer** — Package-private Closeable wrapping MediaPlayer for sample playback.
- **RingtoneUtils** — Public utility with static methods to query system ringtones/alarms/notifications via `RingtoneManager` and music from external storage via `MediaStore`. Handles API 33+ permission split (READ_MEDIA_AUDIO vs READ_EXTERNAL_STORAGE).
- **RingtoneTypes** — Package-private `@IntDef` annotation for type safety.

## Key Details

- **SDK levels**: minSdk 16, targetSdk/compileSdk 33
- **Single dependency**: `androidx.appcompat:appcompat:1.4.2`
- **AGP version**: 4.2.2
- **Permissions**: READ_EXTERNAL_STORAGE declared in library manifest; TYPE_MUSIC requires runtime permission. API 33+ uses READ_MEDIA_AUDIO (handled in `RingtoneUtils.getMediaAudioPermission()`).
- **Publishing**: Configured via `ringtonepicker/bintray.gradle` for Bintray Maven. Requires credentials in `local.properties`.
- **Lint**: `abortOnError` is disabled in the library module.
- **Layout**: Single layout `layout_ringtone_dialog.xml` with a ViewFlipper containing ProgressBar and ListView.
