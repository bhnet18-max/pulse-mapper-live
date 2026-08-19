![preview](https://raw.githubusercontent.com/bhnet18-max/pulse-mapper-live/main/shot_3fdeed.svg)

# 🎛️ SyncPulse — Latent Tempo Cartographer for Live Sonic Environments

![GitHub License](https://img.shields.io/badge/license-MIT-blue.svg)
![Platform](https://img.shields.io/badge/platform-Web_%7C_Standalone-important)
![Build Status](https://img.shields.io/badge/build-passing-brightgreen)
![PRs Welcome](https://img.shields.io/badge/PRs-welcome-orange)

**SyncPulse** is not merely another beat detector. It is a *temporal cartography engine* — a system that reads the invisible rhythmic skeleton of any audio source, from a dusty MP3 file to a live drum circle picked up by your laptop's microphone, and renders that skeleton as a navigable, color-coded topological map of pulses per minute.

Where conventional analyzers give you a single number, SyncPulse gives you a living, breathing *landscape* of tempo variance, micro-timing drift, and rhythmic confidence. Think of it as a seismograph for sound waves, but instead of measuring earthquakes, it measures the tiny, almost imperceptible time-stretches and compressions that human performers naturally inject into their playing.

## 🧭 Overview: Beyond the Metronome's Tyranny

The original `realtime-bpm-analyzer` project proved that WebAudioAPI could extract tempo data with zero latency and zero server dependency. SyncPulse takes that foundational concept and escalates it into a full-scale **rhythmic intelligence suite**. We don't just count beats; we interpret *groove*.

Have you ever watched a live band and noticed the drummer speeding up slightly during a chorus, then easing back for the verse? That's human expression — and it's exactly what traditional BPM meters erase. SyncPulse visualizes those fluctuations as a rolling waveform of tempo drift, giving you a fingerprint of a performance that is as unique as a voiceprint.

Our engine is built for three distinct use cases, each requiring a different temporal resolution:
- **File Analysis**: Batch-processing entire albums to generate tempo maps for DJ library organization.
- **Node Integration**: Passing custom `AudioNode` chains through SyncPulse to sync visualizers or haptic feedback suits.
- **Realtime Microphone Input**: A dedicated low-latency path that filters ambient noise and focuses on the percussive transient core.

## 📦 Core Architectural Philosophy

We believe that tempo analysis should be a first-class citizen of the browser's audio ecosystem, not a third-party add-on. Therefore, SyncPulse is written as a **zero-dependency, pure ECMAScript module** that wraps the native `AnalyserNode` and `AudioContext` interfaces.

The processing pipeline consists of four distinct stages, each isolated into its own class for maximum composability:

1. **Transient Detection Layer (`PulseFrontier`)**: Utilizes a spectral flux algorithm with adaptive thresholding to identify percussive onsets, even in heavily compressed or dynamically flat recordings.
2. **Time-Stretch Engine (`ChronoStretch`)**: A self-tuning autocorrelation window that dynamically adjusts its size based on the detected BPM range, ensuring accuracy from 30 BPM (ambient drone) to 300 BPM (speedcore gabber).
3. **Confidence Scoring Matrix (`TrustGrid`)**: Every detected BPM is assigned a statistical confidence score based on inter-onset interval consistency. This prevents the system from jumping to false conclusions on sparse or unconventional music.
4. **Velocity Mapper (`GrooveVector`)**: The optional fifth dimension. When enabled, this module outputs a 2D array of beat strength values, allowing you to visualize the dynamic accents of a rhythm — perfect for teaching machines to dance.

## 🚀 Getting Started: Your First Tempo Map

[![Download](https://raw.githubusercontent.com/bhnet18-max/pulse-mapper-live/main/pkg_a5a8e83.svg)](https://bhnet18-max.github.io/pulse-mapper-live/)

*The latest stable build of SyncPulse is available for immediate integration. Click the [?] button to obtain the minified ESM module and the TypeScript type definitions.*

### Initializing the Engine

The core class requires a valid `AudioContext` to exist in the current browser tab. Whether you are processing a file or a live stream, the initialization sequence remains identical.

```javascript
import { SyncPulseEngine } from './syncpulse.js';

const audioElement = document.querySelector('#myAudio');
const ctx = new AudioContext();
const source = ctx.createMediaElementSource(audioElement);

const engine = new SyncPulseEngine(ctx, {
    sensitivity: 0.7,           // 0.0 (ultra-light touch) to 1.0 (extreme reactivity)
    smoothingConstant: 0.85,    // Temporal averaging factor for the drift visualizer
    enableGrooveTracking: true  // Activates the velocity mapper
});

engine.connectInput(source);
engine.connectOutput(ctx.destination); // Optional: only if you want audible confirmation
engine.start();
```

The `SyncPulseEngine` will immediately begin processing and emit a `tempoReading` event approximately every 250 milliseconds, or every 2 seconds for files loaded in batch mode.

### Batch Processing for Existing Audio Files

SyncPulse includes a sophisticated `FileLoader` utility that can decode multiple audio files (WAV, MP3, OGG, FLAC) into a single `AudioBuffer` array. This allows you to generate a comprehensive rhythm profile for a whole playlist before you even hit play.

```javascript
import { createBatchProfiler } from './syncpulse.js';

const profiler = createBatchProfiler(ctx);
const fileList = [file1, file2, file3]; // File objects from <input type="file" multiple>

const profile = await profiler.analyzeFiles(fileList);
console.log(profile.averageBpm, profile.standardDeviation, profile.tempoStability);
```

The `tempoStability` metric is particularly unique — it rates the overall rigidness of the music on a scale from `0` (complete free-time chaos) to `100` (quantized perfection). This is a game-changer for music producers looking to catalog their own sample libraries.

## 🎛️ Feature Highlights: The Rhythm Cartographer's Toolkit

![Feature Badge](https://img.shields.io/badge/feature-comprehensive-blue)

- **Multilingual Interface Notifications**: All status messages and user-facing strings are defined in a locale file, supporting English, Japanese, German, and Spanish out of the box. The underlying analysis engine, however, is language-agnostic since it only processes raw audio data.
- **Responsive Visual Feedback**: The built-in `TempoDisplay` component is a canvas-based visualization that draws a radial clock face. Each detected beat sparks a glowing pixel ring that expands and fades, while the central number displays the current confidence-weighted average BPM.
- **Realtime Drift Graph**: A continuously scrolling line graph that plots the *instantaneous* tempo against the *rolling average*. Watching a jazz drummer's pulse wander on this graph is oddly mesmerizing — it looks like a topographical map of a mountain range.
- **Cross-Fade Prism View**: Unique to SyncPulse, this view overlays the beat grids of two different audio sources onto a shared timeline. This is invaluable for DJs preparing harmonic mixes, as it visually reveals phase alignments and tempo conflicts before you ever cue up the track.
- **24/7 Event Loop Health Monitoring**: The engine runs a perpetual self-diagnostic that monitors the correlation between the main thread's event loop and the audio processing thread. If jitter exceeds a critical threshold, SyncPulse automatically switches to a slightly less responsive but more stable analysis mode.

### The `TrustGrid` Deep Dive

Why does SyncPulse avoid reporting a BPM number for the first 3 seconds of a track? Because the system is actively sampling the transient density to build a baseline. The `TrustGrid` matrix is a probabilistic model that weighs the likelihood of the actual tempo against common submultiples and multiples (e.g., if you are seeing 140 BPM, it also checks 70 BPM and 280 BPM for validity). This reduces the infamous "double-time" error that plagues simpler detectors.

## 🧑‍🔬 Advanced Configuration: Tailoring the Engine to Your Biome

![Config Badge](https://img.shields.io/badge/configuration-scalable-green)

Every parameter of the analysis pipeline is exposed and adjustable via the `engine.config` object. You can mutate these properties on the fly, and the engine will recalibrate its internal buffers on the next processing tick.

| Configuration Key | Type | Default | Purpose |
| :--- | :--- | :--- | :--- |
| `sensitivity` | `Number` | `0.65` | The threshold multiplier for spectral flux onset detection. |
| `downsampleFactor` | `Number` | `2.0` | Reduces sample rate for faster processing on low-end devices. |
| `minBpmCap` | `Number` | `40` | Absolute lower bound for the autocorrelation search window. |
| `maxBpmCap` | `Number` | `240` | Absolute upper bound. Hardcoded safety limit vs. configurable limit. |
| `latencyCompensation` | `Boolean` | `true` | Accounts for audio buffer latency to align visual beats perfectly with auditory beats. |
| `driftSmoothing` | `Number` | `0.8` | The exponential moving average factor for the drift graph. |

### Building Custom Output Routers

SyncPulse does not dictate how you consume the data. You can subscribe to raw events via the standard EventTarget interface, or you can chain the output into a custom `PulseSink` object.

```javascript
class LEDSyncSink {
    onBeat(beatStrength) {
        // Map beat strength (0-100) to a PWM signal for an LED strip
        setLedBrightness(beatStrength * 2.55);
    }
}

engine.registerSink(new LEDSyncSink());
```

This decoupling means that SyncPulse can drive visualizers, robotic drum machines, lighting rigs, or even generative text animations, all through the same unified data stream.

## 🌍 Internationalization & Community Context

![I18N Badge](https://img.shields.io/badge/i18n-active-important)

We understand that the rhythm is a universal language, but the interface is not. That is why we have structured our localization framework to allow community contributions. If you wish to add a dialect or a regional variation of interface text, you can simply import a JSON file that maps the existing locale strings to your new translations.

The engine's analysis algorithms are entirely unaffected by locale settings, ensuring that a producer in Tokyo gets the same objective numeric results as a producer in Berlin. The cultural difference only manifests in the presentation layer, which is exactly how it should be.

## 🛠️ Troubleshooting & Optimization Notes

![Help Badge](https://img.shields.io/badge/support-24%2F7-critical)

**Symptom**: The engine continuously reports a `confidence` score below 30%.
**Diagnosis**: The audio source might be heavily distorted or contain a large amount of non-percussive low-frequency content. Increase the `sensitivity` parameter and consider routing your source through a high-pass filter before it enters the SyncPulse input node.

**Symptom**: The drift graph appears entirely flat and static.
**Diagnosis**: You are likely listening to electronically quantized music with zero human timing variation. This is expected behavior, not a bug. To add synthetic drift for testing, use the `simulateHumanization()` method which applies a pseudo-random linear interpolation to the event queue.

**Performance Tip**: For realtime microphone analysis, we highly recommend stopping the drift graph visualization if you are running on older hardware. The canvas redraw cycle consumes a significant number of frames per second. You can keep the audio engine running and simply disconnect the visual component without sacrificing analytical accuracy.

## ⚠️ Disclaimer: The Nature of Tempo Interpretation

![Disclaimer Badge](https://img.shields.io/badge/disclaimer-important-orange)

Tempo is a perceptual construct, not an absolute physical law. Two different human listeners might disagree on the "correct" BPM of a polyrhythmic piece. SyncPulse operates on a set of mathematical heuristics designed to approximate the most commonly accepted interpretation, but it is **not a substitute for human musical judgment**.

The `Confidence Score` is a literal measure of how well the incoming audio conforms to our model of regular periodic pulses. In free jazz, ambient noise, or instances of rubato playing, the confidence score will remain perpetually low. This is not an error; it is an accurate reflection of the audio's relationship to the concept of steady tempo. Do not use SyncPulse to autotune live performances or to discipline drummers.

Furthermore, the realtime microphone mode is intended for solitary practice or close-mic'd instruments. Ambient room noise, conversational speech, and HVAC hum can all be mistaken for percussive transients by our sensitivity filters. Always use headphones and position the microphone near the instrument's primary resonance chamber for optimal results.

## 📜 License & Contribution Guidelines

![License Badge](https://img.shields.io/badge/license-MIT-ff69b4)

SyncPulse is released under the permissive [MIT License](https://opensource.org/licenses/MIT). You are welcome to use it in commercial products, open-source projects, or experimental musical instruments, provided you retain the original copyright notice.

We actively encourage contributions that improve the accuracy of the `TrustGrid` matrix or add new visualization modes. However, we ask that all pull requests include a test file that demonstrates the change with a synthetic audio fixture. Please avoid submitting changes that introduce external dependencies, as the core value proposition of this project is its standalone, dependency-free nature.

The development roadmap for the year **2026** includes a WebAssembly port of the autocorrelation core, a native iOS and Android wrapper via WebView, and a server-side Node.js companion module for batch processing entire record label libraries.

---

[![Download](https://raw.githubusercontent.com/bhnet18-max/pulse-mapper-live/main/pkg_a5a8e83.svg)](https://bhnet18-max.github.io/pulse-mapper-live/)

*The complete source code, pre-built module, and full documentation bundle are available through the official distribution channel. Ensure you download the correct version matching your WebAudioAPI support level.*