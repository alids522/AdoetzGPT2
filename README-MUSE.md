# AdoetzGPT-flutter-enh — Muse enhancement copy

This directory is a full copy of the original
`AdoetzGPT-flutter-enh` (cloned 2026-10-29, HEAD `758fd16`),
made so the original in `/root/tars/AdoetzGPT-flutter-enh` stays untouched.
Do NOT push from here without checking the remote first: the copied
`.git` still points at `alids522/AdoetzGPT-flutter-enh`.

## What changed (all marked `MUSE` in code)

1. **View state preserved** — `lib/screens/app_shell.dart`
   `AnimatedSwitcher` + `ValueKey(currentView)` replaced with `IndexedStack`.
   Switching Chat / Settings / Token Usage / Plugins no longer disposes
   `ChatScreen`, so scroll position and input draft survive.
   Trade-off: all four views stay in memory.

2. **Dead slash commands hidden + feedback** — `lib/screens/chat_screen.dart`
   `_unimplementedSlashCommands` lists the 39 commands whose action is
   `=> false`. They are filtered out of the slash menu, and typing one
   manually now shows a "belum tersedia saat ini" SnackBar instead of
   silently sending it as chat text. Remove a name from that set when
   the command is actually implemented.

3. **Drawer sessions virtualized** — `lib/screens/app_shell.dart`
   The drawer session list changed from `ListView(children: [...map])`
   to `CustomScrollView` + `SliverList.builder`, so only visible session
   tiles are built. Long histories should open the drawer faster.

4. **Chat parse cache** — `lib/screens/chat_screen.dart`
   `_MessageBubble` now caches `parseText` results by message id + exact
   text, capped at 200 entries. Finished messages no longer re-run the
   `<think>` regex on every rebuild; streaming text still re-parses
   because its text changes each chunk, which is correct.

5. **Image decode caps** — `lib/screens/chat_screen.dart`
   Markdown images: `cacheWidth: 1960` (chat lane max 980, 2x).
   Message attachment thumbnails (180x180): `cacheWidth/cacheHeight: 360`.
   Attachment tray thumbnails: `cacheWidth: 240`.
   Fullscreen image dialog is intentionally left uncapped (user zooms).

6. **Pinned dependencies** — `pubspec.yaml`
   `camera: 0.12.0+1` and `syncfusion_flutter_pdf: 33.2.13+1`,
   taken from `pubspec.lock`. They were `any` before and could drift
   on the next resolve. Run `flutter pub get` after pulling this copy
   to confirm the lock still matches.

## Deliberately NOT changed

- No splitting of `AdoetzAppState` (4,956 lines, 126 `notifyListeners`):
  that is the biggest performance win available, but it is a large,
  behaviour-risky refactor and needs a test pass first.
- No message pagination, no Google Fonts bundling change, no settings
  simplification, no accessibility pass. Those were flagged in the
  analysis and remain open.
- No security-related changes, per the original analysis scope.
- No changes to the original directory.

## Verification done 2026-10-29

- Copy verified byte-identical to the original (excluding `.git`)
  before editing.
- After editing, `git diff --stat` in this copy lists only the files
  above. No Flutter SDK was available in this environment to run
  `flutter analyze` / `flutter test`, so run those before building:
  `flutter analyze` and `flutter test`.

## Identity change to AdoetzGPT2 (2026-10-29)

- Android applicationId was already `com.adoetz.adoetzgpt2` in the
  original, so it is kept as-is. Android label changed to `AdoetzGPT2`.
- iOS bundle id changed from `com.adoetz.adoetzgpt` to
  `com.adoetz.adoetzgpt2` (RunnerTests likewise), display name to
  `AdoetzGPT2`, bundle name to `adoetzgpt2`.
- In-app title (MaterialApp) and drawer title changed to `AdoetzGPT2`.
- Web title, manifest name/short_name/description changed to `AdoetzGPT2`.
- NOT changed on purpose: Dart package name (`adoetzgpt`), OAuth
  callback schemes, User-Agent strings, and system-prompt wording.
  Renaming those would break callbacks and backend behaviour, and is
  not needed for a separate Android/iOS install.
- Target new repository: `alids522/AdoetzGPT2` (not the old repo).
  Creation via the GitHub App connector failed with 403 (App token
  cannot create repositories); creation is being retried via the
  user browser session.

## App ID change (2026-10-30)

- Android applicationId changed from com.adoetz.adoetzgpt2 to com.adoetzgpt3.chat, because the original repo already used com.adoetz.adoetzgpt2, so the first AdoetzGPT2 APK still overwrote the old install.
- iOS bundle id aligned to the same com.adoetzgpt3.chat (RunnerTests: com.adoetzgpt3.chat.RunnerTests).
- Android namespace kept at com.adoetz.adoetzgpt2 (Kotlin/R package); only applicationId determines the Android install identity. OAuth schemes (adoetzgpt, com.adoetzgpt) unchanged.
- Display name stays AdoetzGPT2.
