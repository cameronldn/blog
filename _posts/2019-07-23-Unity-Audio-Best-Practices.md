---
title:  "Unity Audio Best Practices"
excerpt_separator: "<!--more-->"
categories:
  - Guides
tags:
  - unity
  - guides
---

This is a quick rundown of audio best practices for Unity, and how not to kill your game with the wrong import settings. 

<!--more-->

Incorrect Audio settings can quickly lead to performance issues, managing your import settings, and planning your audio system early will save countless hours of performance optimisation down the line.

This guide was written using Unity 2019.1.

> Always use uncompressed .wav files as your source audio!

## Load in Background & Preload: 
* Load in Background
	* If enabled the clip loads in the background, preventing the main thread from stalling.
	* If disabled, the scene will not finish loading until all sounds have been loaded.
* Preload Audio Data
	* If enabled, the clip is pre-loaded when the scene is loaded.
	* If disabled, the audio data will be loaded either when a Source attempts to play it, or from an AudioSource.LoadAudioData() call.

| Load In Background | Preload Audio Data | Result |
|--- |--- |--- |
|Enabled|Enabled|As the scene loads, AudioClips will begin loading off the main thread. If the scene finishes loading, and the clips have not, they will continue to load in the background. When a sound is triggered that has not been loaded, it will act as if pre-load was disabled.|
|Enabled|Disabled|When an AudioClip is first called it will load in the background, and then play once it is loaded. Noticeable delay between being called and playing, won't happen on every subsequent play.|
|Disabled|Enabled|Audio is loaded as the scene is loading. The scene will not finish loading until all AudioClips are loaded into memory.|
|Disabled|Disabled|When an AudioClip is first called it will use the main thread to load itself into memory, causing a frame hitch, the larger the sound the longer the hitch.|

## Load Type:
* Streaming
	* Audio Clip will be stored on a device persistent memory (hard drive, flash drive etc) and streamed when played.
	* Uses minimal memory (RAM) but has the highest CPU overhead. More then one streaming clip greatly increases CPU overhead.
	* ‘Streaming CPU’ section in Profiler
* Compressed In Memory
	* Audio Clip will be loaded into memory (RAM) in a compressed state and decompresses while playing.
	* Low RAM and load time, but takes some CPU to decompress.
	* ‘DSP CPU’ in Profiler
* Decompress On Load
	* Audio Clip loaded into memory (RAM) decompressed. This option requires the most memory but playing it uses less CPU than the other Load Types.
	* TL:DR Don’t use on big audio files.

## Compression Format:
* PCM (Pulse-Code Modulation)
	* Highest quality, but at the cost of largest size.
	* Is a raw, uncompressed format.
	* Essentially free, but takes up the most RAM and disk space.
* ADPCM (Adaptive Differential Pulse-Code Modulation)
	* Roughly 3.5 times smaller than PCM has much less CPU overhead compared to Vorbis.
	* Best for larger frequent sounds.
	* Can introduce artifacts and noise into sounds, not the best compression format.
* Vorbis (Ogg Vorbis)
	* Compression ratio can be pushed quite high while maintaining quality.
	* Expensive to compress/decompress on the fly.

## Sample Rate:
* Preserve
	* Uses the sample rate of the raw audio clip.
* Optimise
	* Unity will use the Nyquist theorem to determine the lowest sample rate that can be
used without losing any of the high frequencies. 
	* Example: 
		* If the highest frequency that the sound contains is 10kHz, the sample rate can be lowered to 20kHz without any loss of sound content. This setting can only be used with PCM & ADPCM.
* Override
	* Override the audio clips default sample rate with a custom setting.

## Recommended Settings
### PC / Console

| Sound | Type | Load In Background | Preload | Format |
|--- |--- |--- |--- |--- |
|Music|Streaming|n/a|n/a|Vorbis (80%)|
|Ambience(long)|Streaming / Decompress|Yes|No|Vorbis (70%)|
|Environmental (short)|Decompress|Yes|Yes|Vorbis (70%)|
|Music (one shot)|Compressed|Yes|Yes|Vorbis (85%)|
|Foley|Compressed|Yes|Yes|PCM|
|Non-Dialogue VO|Decompress|Yes|Yes|Vorbis (70%)|
|Dialogue|Compressed|Yes|Yes|Vorbis (70%)|
|FX (long)|Decompress|No|Yes|Vorbis (70%)|
|FX (short)|Compressed|Yes|Yes|PCM|
|UI (long)|Decompress|No|Yes|Vorbis (70%)|
|UI (short)|Compressed|Yes|Yes|PCM|

### Mobile

| Sound | Type | Load In Background | Preload | Format |
|--- |--- |--- |--- |--- |
|Music|Streaming|n/a|n/a|Vorbis (65%)|
|Ambience(long)|Compressed|Yes|Yes|Vorbis (40%)|
|Environmental (short)|Decompress|Yes|Yes|Vorbis (45%)|
|Music (one shot)|Compressed|Yes|Yes|Vorbis (65%)|
|Foley|Compressed|No|Yes|PCM/ADPCM|
|Non-Dialogue VO|Decompress|Yes|Yes|Vorbis (45%)|
|Dialogue|Compressed|Yes|Yes|Vorbis (45%)|
|FX (long)|Decompress|No|Yes|Vorbis (45%)|
|FX (short)|Compressed|Yes|Yes|PCM/ADPCM|
|UI (long)|Decompress|No|Yes|Vorbis (45%)|
|UI (short)|Compressed|Yes|Yes|PCM/ADPCM|

## Sources:

[Rush VR](http://thebinarymill.com/rush.php)

[Wrong Import Settings are Killing Your Unity Game](https://blog.theknightsofunity.com/wrong-import-settings-killing-unity-game-part-2/)

[Unity Audio Import Optimisation - getting more BAM for your RAM](https://www.gamasutra.com/blogs/ZanderHulme/20190107/333794/Unity_Audio_Import_Optimisation__getting_more_BAM_for_your_RAM.php#2)

[Making Your Unity Game Scream and Shout and Not Killing It in the Process](https://medium.com/@a.mstv/making-your-unity-game-scream-and-shout-and-not-killing-it-in-the-process-673a7384693c)


