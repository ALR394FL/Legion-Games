# Legion Games Arcade

A browser-based, 100% offline-capable 3-game arcade launcher built for American Legion Post 394 game nights—optimized for large-screen TVs, laptops, and Android TV boxes.

## How to Play
* **Play Online (GitHub Pages):** Open [https://alr394fl.github.io/Legion-Games/index.html](https://alr394fl.github.io/Legion-Games/index.html) directly in any modern web browser (Chrome, Edge, Firefox, Safari) with zero setup required.
* **Play Offline Locally:** Clone or download this repository as a ZIP file and open `index.html` directly in your browser.
* **Download Precompiled Android TV APK:** Grab the latest `.apk` directly from the [Releases page](https://github.com/ALR394FL/Legion-Games/releases/latest) and sideload it onto your Android TV box.
* **Build Android TV APK from Source:** Follow the headless Linux Capacitor build guide below to compile and deploy your own offline kiosk APK.

## Included Games
* **Bingo Master Board:** Full-featured Bingo caller and master board (forked from Timothy Hsu's *Bingo Master Board*, MIT License).
* **Steak Shoot:** Interactive 52-card draw board with enlarged active-card highlighting, double-click protection, and Text-to-Speech card announcements.
* **Show Me the Money:** 52-card draw board featuring configurable ticket counts and a slide-out **2-of-3 whole-dollar payout split** calculator (Winner / Post / Jackpot).

## Features
* **100% Offline Ready:** All HTML, CSS, JavaScript, and local state storage (`localStorage`) run completely offline with automatic mid-game recovery after a refresh or power cycle.
* **TV & Mouse Friendly:** Large high-contrast UI, on-screen Home navigation, fullscreen toggle buttons, spacebar card drawing, and a 3-second draw cooldown to prevent accidental mouse double-clicks.
* **Offline Text-to-Speech:** Supports native Android TTS (via `@capacitor-community/text-to-speech`) with automatic fallback to standard browser speech synthesis.

---

## Android TV APK Build & Update Guide (Linux Headless)

### Prerequisites on Linux Build Box
* **Java 21** (`openjdk-21-jdk`)
* **Node.js & npm**
* **Android SDK Command-Line Tools** (`~/android-sdk` with `platform-tools`, `platforms;android-34`, `build-tools;34.0.0`)

### First-Time Setup & Initial Build
Run these commands on your Linux build machine to clone the repository, wrap it in Capacitor, and install the Text-to-Speech plugin:

```bash
mkdir -p ~/tv-arcade && cd ~/tv-arcade
git clone https://github.com/ALR394FL/Legion-Games.git www

npm init -y
npm install @capacitor/core @capacitor/cli @capacitor/android @capacitor-community/text-to-speech
npx cap init "Arcade Games" "com.custom.arcadegames" --web-dir www
npx cap add android
```

### Android Native & Kiosk Tweaks (First-Time Only)
* **`AndroidManifest.xml`:**
  * Add `LEANBACK_LAUNCHER` category and `<uses-feature android:name="android.hardware.touchscreen" android:required="false" />` for Android TV compatibility.
  * Add `<queries>` intent for `android.intent.action.TTS_SERVICE` above `<application>` so Android 11+ allows binding to offline TTS engines.
* **`MainActivity.java`:** Override `onBackPressed()` with an empty method so mouse right-clicks and hardware Back presses do not exit the app.
* **System Offline TTS Engine (Android TV / SlimBOX):** If your firmware does not include a built-in Text-to-Speech engine, sideload an offline TTS engine APK (for example, **Next-gen Kaldi / Sherpa-ONNX** was used in this setup, though other offline engines like RHVoice or eSpeak NG may work—*YMMV*) and set it as default via ADB:
  ```bash
  adb connect <YOUR_TV_IP>:5555
  adb shell settings put secure tts_default_synth <TTS_ENGINE_PACKAGE_NAME>
  ```
* **SlimBOX Kiosk Lockdown:** Configure your kiosk launcher to auto-boot **Arcade Games**, set a PIN on Android Settings, and hide the bottom system navigation bar in SlimBOX display settings.

### One-Line Update, Rebuild & ADB Deploy
Whenever changes are pushed to GitHub later, pull the latest web files, sync Capacitor, compile the APK, and push it over Wi-Fi to the Android TV box:

```bash
cd ~/tv-arcade/www && git pull && cd .. && npx cap sync android && cd android && ./gradlew assembleDebug && adb connect <YOUR_TV_IP>:5555 && adb install -r app/build/outputs/apk/debug/app-debug.apk
```

---

## Credits & License
* **Bingo Master Board:** Created by Timothy Hsu ([https://github.com/TimTree/bingo-master-board](https://github.com/TimTree/bingo-master-board)), licensed under the **MIT License**.
* **Steak Shoot:** Created by Mrmazakblu and Google Gemini.
* **Show Me the Money:** Created by Mrmazakblu and Google Gemini.
