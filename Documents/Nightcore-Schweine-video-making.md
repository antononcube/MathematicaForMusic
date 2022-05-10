# Nightcore "Schweine" video making

Anton Antonov
[MathematicaForPrediction at WordPress](http://mathematicaforprediction.wordpress.com)
[MathematicaForPrediction at GitHub](https://github.com/antononcube/MathematicaForPrediction)
May 2022

## Introduction

This notebook shows how to make [Nightcore](https://en.wikipedia.org/wiki/Nightcore) modifications to a song video. We use [Glukoza's](http://www.glukoza.com) song "Schweine". The song "Schweine" became popular via the [radio station Vladivostok FM](https://gta.fandom.com/wiki/Radio_Stations_in_GTA_IV#Vladivostok_FM) of the game ["Grand Theft Auto IV"](https://en.wikipedia.org/wiki/Grand_Theft_Auto_IV). 

**Remark:** We use Schweine since its licencing allows copies of it to be (publicly) played [via YouTube](https://www.youtube.com/watch?v=8UsR9L3KPIU).

The Nightcore transformation of the song was fairly straightforward with Mathematica / WL. The video transformation and combination are also fairly straightforward or easy.

**Remark:** Here is the final result uploaded to YouTube (https://www.youtube.com/watch?v=8UsR9L3KPIU):

[![](https://github.com/antononcube/MathematicaForMusic/raw/master/Documents/Diagrams/Nightcore-Schweine-video-making/0ffx6fronoawk.png)]( https://www.youtube.com/watch?v=8UsR9L3KPIU)


## Get movies

Here is a link to the official video: https://www.youtube.com/watch?v=Ue5ZBe-GzSM .

Here is a link to a version with English subtitles: https://www.youtube.com/watch?v=9Es1nPWzJ-0 .

Download at least one of the videos. (Use a Firefox or Google Chrome plugin; use [VLC](https://www.videolan.org/vlc/); or utilize the paclet described in the post ["Playing with YouTube from Mathematica"](https://b3m2a1.github.io/playing-with-youtube-from-mathematica.html), [BMI1].)

Here we import the downloaded video:

```mathematica
vdSubSchweine = Import["~/Downloads/Glukoza Nostra - Schweine -subtitled-.mp4"]
```

![](https://github.com/antononcube/MathematicaForMusic/raw/master/Documents/Diagrams/Nightcore-Schweine-video-making/1ow21rc06glgb.png)

## Make Nightcore audio

The process for making a song Nightcore is described in Wikipedia, [Wk1]. Basically, we just make the tempo 20-30% faster and raise the pitch with, ≈5.5 semitones.

**Remark:** An alternative of the process shown in this section is to use audio transformation programs like [Audacity](https://www.audacityteam.org) and [AmadeusPro](https://apps.apple.com/us/app/amadeus-pro/id438292371?mt=12).

Here we get the audio from the video:

```mathematica
auSchweine = Audio[vdSubSchweine]
```

![](https://github.com/antononcube/MathematicaForMusic/raw/master/Documents/Diagrams/Nightcore-Schweine-video-making/1uuz3gsogs93f.png)

Here we change the tempo to be 20% faster: 

```mathematica
AbsoluteTiming[
  auSchweineFaster = AudioTimeStretch[auSchweine, 1/1.2] 
 ]
```

![](https://github.com/antononcube/MathematicaForMusic/raw/master/Documents/Diagrams/Nightcore-Schweine-video-making/1n7u00nxdhs7q.png)

Here we raise the pitch with $5.5$ semitones:

```mathematica
AbsoluteTiming[
  auSchweineNightcore = AudioPitchShift[auSchweineFaster, Quantity[5.5, "Semitones"]] 
 ]
```

![](https://github.com/antononcube/MathematicaForMusic/raw/master/Documents/Diagrams/Nightcore-Schweine-video-making/005j44hxpz7h0.png)

## Get lyrics

Although, we have a video with English subtitles, it would be interesting to experiment with adding subtitles to the video or "discovering" the subtitles in the video frames.

Instead of just copy-&-pasting the text I took screenshot of lyrics here: https://lyrics-on.net/en/1023698-schweine-shvajjne-lyrics.html .

![](https://github.com/antononcube/MathematicaForMusic/raw/master/Documents/Diagrams/Nightcore-Schweine-video-making/0jnweyevtrddx.png)

Here the image above is split into two halves and they displayed in a grid:

```mathematica
imgLyrics1 = ImageTake[imgLyrics, All, {1, ImageDimensions[imgLyrics][[2]]/2}];
imgLyrics2 = ImageTake[imgLyrics, All, {ImageDimensions[imgLyrics][[2]]/2, -1}];
GraphicsGrid[{{imgLyrics1, imgLyrics2}}, Dividers -> All, ImageSize -> 700]
```

![](https://github.com/antononcube/MathematicaForMusic/raw/master/Documents/Diagrams/Nightcore-Schweine-video-making/0nok801otpf32.png)

Here we recognize the lyrics within each half:

```mathematica
Grid[{{TextRecognize[imgLyrics1, Language -> "Russian"], TextRecognize[imgLyrics2, Language -> "English"]}}, Dividers -> All]
```

![](https://github.com/antononcube/MathematicaForMusic/raw/master/Documents/Diagrams/Nightcore-Schweine-video-making/07s8wyhharkul.png)

**Remark:** Because we found a video with subtitles, we do not use further the extracted lyrics in this notebook.

## Direct video styling

If we only wanted to change how the video *looks* we can directly manipulate the video frames with VideoFrameMap, [WRI6] :

```mathematica
AbsoluteTiming[
  k = 0; 
  vdSchweine4 = VideoFrameMap[Switch[Mod[k++, 500] < 250, True, EdgeDetect[#], False, ImageEffect[#, "EdgeStylization"]] &, vdSubSchweine]; 
 ]
```

```mathematica
vdSchweine4
```

![](https://github.com/antononcube/MathematicaForMusic/raw/master/Documents/Diagrams/Nightcore-Schweine-video-making/1x9obucooutc8.png)

**Remark:** Since we want to make both the audio and video shorter we have to use video frames.

## Make Nightcore video

Get the frames of the video:

```mathematica
AbsoluteTiming[
  lsFrames = VideoExtractFrames[vdSubSchweine, All]; 
 ]

(*{11.5501, Null}*)
```

Show the number of frames:

```mathematica
Length[lsFrames]

(*9041*)
```

Change all the frames to have the "Sepia" image effect:

```mathematica
AbsoluteTiming[
  lsFramesSepia = ParallelMap[ImageEffect[#, "Sepia"] &, lsFrames]; 
 ]

(*{124.898, Null}*)
```

Generate (audio-less) video from the list of frames that have the same length as the generated audio:

```mathematica
AbsoluteTiming[
  vdSubSchweineNew = VideoGenerator[lsFramesSepia, Duration[auSchweineNightcore]]; 
 ]

(*{115.34, Null}*)
```

Combine the video and audio (into a new video):

```mathematica
AbsoluteTiming[
  vdSubSchweineNightcore = VideoCombine[{vdSubSchweineNew, auSchweineNightcore}]; 
 ]

(*{0.576532, Null}*)
```

```mathematica
vdSubSchweineNightcore
```

![](https://github.com/antononcube/MathematicaForMusic/raw/master/Documents/Diagrams/Nightcore-Schweine-video-making/0ffx6fronoawk.png)

**Remark:** Here we do not export the video, since Mathematica saves it in a "standard" location of the host operating system.

## References

[BMA1]  b3m2ma1, ["Playing with YouTube from Mathematica"](https://community.wolfram.com/groups/-/m/t/1484511), (2018), Wolfram Community. ([GitHub link](https://b3m2a1.github.io/playing-with-youtube-from-mathematica.html).)

[Wk1] Wikipedia entry, ["Nightcore"](https://en.wikipedia.org/wiki/Nightcore).

[WRI1] Wolfram Research (2010), TextRecognize, Wolfram Language function, https://reference.wolfram.com/language/ref/TextRecognize.html (updated 2020).

[WRI2] Wolfram Research (2016), Audio, Wolfram Language function, https://reference.wolfram.com/language/ref/Audio.html (updated 2020).

[WRI3] Wolfram Research (2016), AudioTimeStretch, Wolfram Language function, https://reference.wolfram.com/language/ref/AudioTimeStretch.html (updated 2020).

[WRI4] Wolfram Research (2016), AudioPitchShift, Wolfram Language function, https://reference.wolfram.com/language/ref/AudioPitchShift.html (updated 2020).

[WRI5] Wolfram Research (2020), VideoExtractFrames, Wolfram Language function, https://reference.wolfram.com/language/ref/VideoExtractFrames.html.

[WRI6] Wolfram Research (2020), VideoFrameMap, Wolfram Language function, https://reference.wolfram.com/language/ref/VideoFrameMap.html (updated 2021).

[WRI7] Wolfram Research (2008), ImageEffect, Wolfram Language function, https://reference.wolfram.com/language/ref/ImageEffect.html (updated 13).

[WRI8] Wolfram Research (2020), VideoGenerator, Wolfram Language function, https://reference.wolfram.com/language/ref/VideoGenerator.html (updated 2021).

[WRI9] Wolfram Research (2020), VideoCombine, Wolfram Language function, https://reference.wolfram.com/language/ref/VideoCombine.html.
