# HCPlayer - VideoPlayer

 playerView            |  playerview landscap
:-------------------------:|:-------------------------:
![](https://github.com/HC005/HCPlayer/blob/master/device-2021-02-18-172743.png)  |  ![](https://github.com/HC005/HCPlayer/blob/master/device-2021-02-18-172820.png)

# Features
 - Support different stream type including [Progressive, HLS, DASH, SmoothStreaming](https://exoplayer.dev/media-sources.html)
 - Play list of videos or resume video from last watched position
 - Support different video qualities
 - Switch between different <b>subtitles</b>
 - Cache video
 - Lock player screen
 - Forward and backward by double tap on screen
 - Mute mode
 - Loop toggle mode
 - AndroidX version project
 
 # Get started

 ## 1. Dependencies
 In this project we used Exoplayer v.2.11.3


## 2. Create an instance of VideoPlayer in your activity

```java
 // VideoSource is a list of SingleVideo which contains list of videos, their subtitles &
 // their last watched length (used to resume the video)
 player = new VideoPlayer(playerView, getApplicationContext(), videoSource, this);

 // Used to pause/resume player on incoming calls
 mAudioManager = (AudioManager) getApplicationContext().getSystemService(Context.AUDIO_SERVICE);

 // Used to hide/show player controller views
 playerView.setControllerVisibilityListener(visibility -> {
    if (player.isLock())
        playerView.hideController();
        back.setVisibility(visibility == View.VISIBLE && !player.isLock() ? View.VISIBLE : View.GONE);
 });

 // Optional setting
 playerView.getSubtitleView().setVisibility(View.GONE);
 player.seekToOnDoubleTap();
```
    
## 3. Initialize ExoPlayer
Initialize your ExoPlayer in VideoPlayer as follow :
 
 ```java
  cacheDataSourceFactory = new CacheDataSourceFactory(
     context, 100 * 1024 * 1024, 5 * 1024 * 1024);

  trackSelector = new DefaultTrackSelector(context);

  exoPlayer = new SimpleExoPlayer.Builder(context)
                  .setTrackSelector(trackSelector)
                  .build();

  playerView.setPlayer(exoPlayer);
  playerView.setKeepScreenOn(true);
  exoPlayer.setPlayWhenReady(true);
  // Add a listener to receive events from the player.
  exoPlayer.addListener(componentListener);
  // Build mediaSource depend on video type (Regular, HLS, DASH, etc)
  mediaSource = buildMediaSource(videoSource.getVideos().get(index), cacheDataSourceFactory);
  exoPlayer.prepare(mediaSource);
  // Resume video
  seekToSelectedPosition(videoSource.getVideos().get(index).getWatchedLength(), false);
```
## 4. Add listener
Add listener implementations for player control buttons in your activity.

## 5. Subtitle

 Subtitle            |  playerview with subtitle
:-------------------------:|:-------------------------:
![](https://github.com/HC005/HCPlayer/blob/master/device-2021-02-18-173028.png)  |  ![](https://github.com/HC005/HCPlayer/blob/master/device-2021-02-18-173706.png)


As mentioned in [ExoPlayer Doc](https://exoplayer.dev/media-sources.html):
Given a video file and a separate subtitle file, MergingMediaSource can be used to merge them into a single source for playback.

```java
  Format subtitleFormat = Format.createTextSampleFormat(
                id, // can be null
                MimeTypes.APPLICATION_SUBRIP,
                Format.NO_VALUE,
                null);

  MediaSource subtitleSource = new SingleSampleMediaSource
                                .Factory(cacheDataSourceFactory)
                                .createMediaSource(subtitleUri, subtitleFormat, C.TIME_UNSET);

  exoPlayer.prepare(new MergingMediaSource(mediaSource, subtitleSource),
   false, // Reset position
   false //Reset state
  );
```

## Version notes

#### V.1.1.0 (27.3.2020)
- Migrate to Androidx
- Exoplayer v.2.11.3
- Customized next/previous buttons
- Fix bugs:
    - Resume video using last watched position
    - playing list of videos
    - Unlock player

#### V.1.0.0
- Exoplayer v.2.9.2
