# WebViewApp - Android WebView Wrapper for http://zaskarian.com

This is a native Android WebView app that loads your website.

## Quick Start: Build APK via GitHub Actions (No Local Android SDK Needed)

### Option 1: GitHub Actions (Recommended - Free Cloud Build)

1. **Create a GitHub repository** and push this project:
   ```bash
   cd /var/www/main-android
   git init
   git add .
   git commit -m "Initial commit"
   git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO.git
   git push -u origin main
   ```

2. **Enable GitHub Actions** in your repository settings (Actions → General → Allow all actions)

3. **Trigger a build**:
   - Push to main/master branch, OR
   - Go to Actions tab → "Build Android APK" → "Run workflow", OR
   - Create a tag: `git tag v1.0.0 && git push origin v1.0.0`

4. **Download the APK** from Actions artifacts (available for 30 days) or from Releases if you pushed a tag

### Option 2: Local Build (Requires Android Studio)

```bash
# Install Android Studio with SDK 34
# Or use command line:
sdkmanager "platforms;android-34" "build-tools;34.0.0" "platform-tools"

# Build
./gradlew assembleDebug      # Debug APK
./gradlew assembleRelease    # Release APK (unsigned)
```

**Output locations:**
- Debug: `app/build/outputs/apk/debug/app-debug.apk`
- Release: `app/build/outputs/apk/release/app-release-unsigned.apk`

### Option 3: GitHub Codespaces (Free Cloud IDE)

1. Open your repo in GitHub Codespaces
2. Run: `./gradlew assembleDebug`
3. Download the APK from the Codespaces file explorer

## Project Structure

```
WebViewApp/
├── app/
│   ├── src/main/
│   │   ├── java/com/example/webviewapp/
│   │   │   └── MainActivity.kt      # WebView logic
│   │   ├── res/
│   │   │   ├── layout/activity_main.xml
│   │   │   ├── values/strings.xml, colors.xml, themes.xml
│   │   │   └── xml/backup_rules.xml, data_extraction_rules.xml
│   │   └── AndroidManifest.xml
│   └── build.gradle.kts
├── .github/workflows/build.yml      # GitHub Actions CI/CD
├── build.gradle.kts
├── settings.gradle.kts
├── gradle.properties
└── gradle/wrapper/                  # Gradle wrapper (no install needed)
```

## Configuration

### Change Target URL
Edit `app/src/main/java/com/example/webviewapp/MainActivity.kt`:
```kotlin
private val targetUrl = "http://zaskarian.com"  // Change this
```

### App Name & Icon
- **Name**: `app/src/main/res/values/strings.xml` → `app_name`
- **Icon**: Replace files in `app/src/main/res/mipmap-*/ic_launcher.png`

### Permissions (in AndroidManifest.xml)
- `INTERNET` - Required for WebView
- `ACCESS_NETWORK_STATE` - Network detection
- `ACCESS_WIFI_STATE` - WiFi detection

## Features

- ✅ Full WebView with JavaScript enabled
- ✅ Pull-to-refresh (swipe down)
- ✅ Back button navigation (goes back in WebView history)
- ✅ Handles tel:/mailto:/intent: links externally
- ✅ Mixed content support (HTTP in HTTPS)
- ✅ Zoom controls (pinch to zoom)
- ✅ Cache enabled for offline support
- ✅ Material Design theme
- ✅ Dark mode support
- ✅ Full screen (no action bar)

## Signing Release APK (for Play Store)

```bash
# Generate keystore (do once)
keytool -genkey -v -keystore my-release-key.jks -keyalg RSA -keysize 2048 -validity 10000 -alias my-alias

# Sign APK
apksigner sign --ks my-release-key.jks --out app-release-signed.apk app/build/outputs/apk/release/app-release-unsigned.apk

# Verify
apksigner verify app-release-signed.apk
```

Or configure signing in `app/build.gradle.kts`:
```kotlin
signingConfigs {
    create("release") {
        storeFile = file("my-release-key.jks")
        storePassword = "your-store-password"
        keyAlias = "my-alias"
        keyPassword = "your-key-password"
    }
}
buildTypes {
    release {
        signingConfig = signingConfigs.getByName("release")
    }
}
```

## Troubleshooting

| Issue | Solution |
|-------|----------|
| White screen | Check URL uses HTTPS, enable mixed content in WebView |
| Links don't open | Add `android:usesCleartextTraffic="true"` in manifest |
| SSL errors | Ensure valid certificate, or handle in WebViewClient |
| Build fails | Check Java 17, Android SDK 34, Gradle 8.5 |

## License

MIT - Feel free to use and modify.