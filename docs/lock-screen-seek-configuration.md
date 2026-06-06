# Lock Screen Seek Configuration

## New functionality

`AudioPro.configure()` now accepts `disableLockScreenSeek?: boolean`.

When `disableLockScreenSeek` is `true`, iOS disables `MPRemoteCommandCenter.shared().changePlaybackPositionCommand`. This hides or prevents use of the lock screen playback progress scrubber while preserving the existing play, pause, toggle, next/previous, and skip-forward/back command behavior.

The default is `false`, so existing apps keep the current lock screen scrubber behavior unless they opt out.

## JavaScript and TypeScript changes

- Added `disableLockScreenSeek?: boolean` to `AudioProConfigureOptions` in `src/types.ts`.
- Added `disableLockScreenSeek: false` to `DEFAULT_CONFIG` in `src/values.ts`.
- Documented the option in the `AudioPro.configure()` JSDoc in `src/audioPro.ts`.
- Verified the bridge path with a Jest test: `AudioPro.configure({ disableLockScreenSeek: true })` is stored in configuration and included in the native options object passed to `NativeModules.AudioPro.play()`.

## iOS native changes

- Added a stored Swift setting, `settingDisableLockScreenSeek`, in `ios/AudioPro.swift`.
- Parsed `options["disableLockScreenSeek"]` from the native playback options dictionary, defaulting to `false`.
- Applied the setting in `setupRemoteTransportControls()` with:

```swift
commandCenter.changePlaybackPositionCommand.isEnabled = !settingDisableLockScreenSeek
```

- Split remote command setup into setting application and target registration so command enabled states refresh on each `AudioPro.play()` call without registering duplicate command handlers.

## Usage

```typescript
AudioPro.configure({
	disableLockScreenSeek: true,
});
```

Configuration changes are still applied through the existing library lifecycle: configure options are stored in JavaScript and sent to native playback options on the next `AudioPro.play()` call.
