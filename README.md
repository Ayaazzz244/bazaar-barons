# Bazaar Barons

An original property-trading board game for Android. 3 or 4 players (pass-and-play, any seat can be a CPU), verifiable fair dice, AdMob banner + interstitial + rewarded ads.

## Get the APK (no Android Studio needed)
1. Create an empty GitHub repository and upload everything in this folder (keep the `.github` folder).
2. Open the repo's **Actions** tab. The "Build Android APK" workflow runs on every push to `main` (or press **Run workflow**).
3. When it finishes (about 5 minutes), open the run and download **bazaar-barons-apk**. Inside is `app-debug.apk`. Copy it to your phone and install it (allow "install unknown apps").

## Build on your own PC instead
Install Node 22, JDK 21 and Android Studio, then:
```
npm ci
npx cap sync android
npx cap open android      # then Build > Build APK(s)
```

## Where to put your real AdMob IDs
| What | File | Line |
|---|---|---|
| App ID (`ca-app-pub-...~...`) | `android/app/src/main/AndroidManifest.xml` | `com.google.android.gms.ads.APPLICATION_ID` |
| Banner / interstitial / rewarded unit IDs (`ca-app-pub-.../...`) | `www/index.html` | `AD_CONFIG` at the top of the script |
| Turn off test mode | `www/index.html` | `AD_CONFIG.testing = false` |

After editing, run `npx cap sync android` (the GitHub workflow does this for you).

## Signed release for Google Play
1. Create an upload key once (keep it safe, you need it for every update):
   `keytool -genkeypair -v -keystore upload.jks -alias upload -keyalg RSA -keysize 2048 -validity 10000`
2. Either create `android/keystore.properties` (never commit it):
   ```
   storeFile=/full/path/upload.jks
   storePassword=...
   keyAlias=upload
   keyPassword=...
   ```
   and run `cd android && ./gradlew bundleRelease`,
   or add GitHub secrets `KEYSTORE_BASE64` (`base64 -w0 upload.jks`), `KEYSTORE_PASSWORD`, `KEY_ALIAS`, `KEY_PASSWORD` and the workflow also builds `app-release.aab`.
3. Upload `android/app/build/outputs/bundle/release/app-release.aab` to Play Console.
4. Bump `versionCode` (and `versionName`) in `android/app/build.gradle` for every update.

## Store assets
`store/icon-512.png` (Play icon) and `store/feature-graphic-1024x500.png`.

## Game ad placements
- **Banner**: bottom of the screen, always on during play.
- **Interstitial**: at most once every 3 minutes, only at natural breaks (start of a new round, game over, quitting to menu).
- **Rewarded**: "🎁 Bonus" (+₹150, once per player per game) and "📺 Ad bailout" (+₹300 when a player can't pay, once per game).
- `AD_CONFIG.removeAdsPurchased` is the hook for a "Remove Ads" in-app purchase.
