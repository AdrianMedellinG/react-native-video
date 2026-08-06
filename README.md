# react-native-video
🎬 `<Video>` component for React Native

## Documentation
documentation is available at [thewidlarzgroup.github.io/react-native-video/](https://thewidlarzgroup.github.io/react-native-video/)

## Usage

```javascript
// Load the module

import Video, {VideoRef} from 'react-native-video';

// Within your render function, assuming you have a file called
// "background.mp4" in your project. You can include multiple videos
// on a single screen if you like.

const VideoPlayer = () => {
 const videoRef = useRef<VideoRef>(null);
 const background = require('./background.mp4');

 return (
   <Video 
    // Can be a URL or a local file.
    source={background}
    // Store reference  
    ref={videoRef}
    // Callback when remote video is buffering                                      
    onBuffer={onBuffer}
    // Callback when video cannot be loaded              
    onError={onError}               
    style={styles.backgroundVideo}
   />
 )
}

// Later on in your styles..
var styles = StyleSheet.create({
  backgroundVideo: {
    position: 'absolute',
    top: 0,
    left: 0,
    bottom: 0,
    right: 0,
  },
});
```

## Android MP2 audio support

This fork can enable MP2 / MPEG Layer II audio playback on Android by using
ExoPlayer's FFmpeg extension renderer path.

MP2 support is not enabled by JavaScript props. Your `Video` source stays the
same:

```tsx
<Video
  source={{
    uri: 'https://example.com/live/playlist.m3u8',
  }}
  onError={onError}
  style={styles.video}
/>
```

The Android app that consumes this fork must enable the FFmpeg renderer and add
a Media3 FFmpeg decoder extension.

### 1. Enable the renderer in `android/build.gradle`

In the app-level React Native Android project, set `useExoplayerFfmpeg = true`
in the root `android/build.gradle` `ext` block:

```gradle
buildscript {
  ext {
    media3Version = "1.2.1"
    useExoplayerFfmpeg = true

    // Keep your existing react-native-video ExoPlayer flags as needed:
    useExoplayerHls = true
    useExoplayerDash = true
    useExoplayerSmoothStreaming = true
    useExoplayerRtsp = true
  }
}
```

When enabled, this fork configures ExoPlayer with
`EXTENSION_RENDERER_MODE_PREFER`, so FFmpeg audio renderers are preferred when
available.

### 2. Add an FFmpeg decoder to `android/app/build.gradle`

The app must package an FFmpeg decoder implementation. One tested option is the
Jellyfin Media3 FFmpeg decoder:

```gradle
android {
  compileOptions {
    coreLibraryDesugaringEnabled true
  }
}

dependencies {
  implementation "org.jellyfin.media3:media3-ffmpeg-decoder:1.2.1+1"
  coreLibraryDesugaring "com.android.tools:desugar_jdk_libs:2.0.4"
}
```

Use an FFmpeg decoder built for the same Media3 version your app uses. MP2 is
handled by FFmpeg's `mp3` decoder family, so custom FFmpeg builds must include
that decoder.

### 3. Test with the included example

The example app has a test stream and a local MP2 asset configured in
`examples/basic/src/VideoPlayer.tsx`.

```powershell
cd examples/basic/android
.\gradlew.bat :app:installRelease --no-daemon --console=plain
adb shell am start -n com.videoplayer/.MainActivity
```

If a debug build closes immediately on newer Android versions because of React
Native DevSupport / Inspector, use `installRelease` for playback testing.

### Notes

- This MP2 path is Android-only.
- iOS uses AVPlayer/AVFoundation and cannot be extended the same way from this
  library alone.
- The Jellyfin FFmpeg decoder is GPL-licensed. Review its license before using
  it in a production app, or build/package your own Media3 FFmpeg extension.

## Community support
We have an discord server where you can ask questions and get help. [Join the discord server](https://discord.gg/WXuM4Tgb9X)

## Enterprise Support
<p>
  📱 <i>react-native-video</i> is provided <i>as it is</i>. For enterprise support or other business inquiries, <a href="https://www.thewidlarzgroup.com/">please contact us 🤝</a>. We can help you with the integration, customization and maintenance. We are providing both free and commercial support for this project. let's build something awesome together! 🚀
</p>
<a href="https://www.thewidlarzgroup.com/">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="./docs/assets/baners/twg-dark.png" />
    <source media="(prefers-color-scheme: light)" srcset="./docs/assets/baners/twg-light.png" />
    <img alt="TheWidlarzGroup" src="./docs/assets/baners/twg-light.png" />
  </picture>
</a>
