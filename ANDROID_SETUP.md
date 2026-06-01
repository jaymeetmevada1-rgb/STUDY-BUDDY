# Android Wrapper Setup Guide

## Project Structure

Your Study Buddy app has been converted to an Android wrapper that loads your web app in a WebView.

## Prerequisites

1. **Android Studio** - Download from [developer.android.com](https://developer.android.com/studio)
2. **Java Development Kit (JDK)** - Version 11 or later
3. **Android SDK** - API level 34

## Setup Instructions

### 1. Open Project in Android Studio

```bash
# Clone your repository
git clone https://github.com/jaymeetmevada1-rgb/STUDY-BUDDY.git
cd STUDY-BUDDY
git checkout android-wrapper
```

Then open the project in Android Studio:
- File → Open → Select the project folder

### 2. Configure Web App URL

Edit `app/src/main/java/com/studybuddy/app/MainActivity.java`:

```java
// Change this line to your actual website URL:
webView.loadUrl("https://your-domain.com");

// OR use local HTML files:
// webView.loadUrl("file:///android_asset/index.html");
```

### 3. Update App Details

Edit `app/build.gradle`:

```gradle
defaultConfig {
    applicationId "com.studybuddy.app"  // Change to unique package name
    versionCode 1
    versionName "1.0"
}
```

### 4. Add App Icons (Optional)

Place your app icons in:
- `app/src/main/res/mipmap-hdpi/ic_launcher.png`
- `app/src/main/res/mipmap-xhdpi/ic_launcher.png`
- `app/src/main/res/mipmap-xxhdpi/ic_launcher.png`

Use [Android Asset Studio](https://romannurik.github.io/AndroidAssetStudio/) to generate icons.

## Building the App

### Build APK (for testing)

```bash
./gradlew assembleRelease
```

Output: `app/build/outputs/apk/release/app-release-unsigned.apk`

### Build AAB (for Google Play Store - RECOMMENDED)

```bash
./gradlew bundleRelease
```

Output: `app/build/outputs/bundle/release/app-release.aab`

## Signing the App

Before publishing, you need to sign your app with a keystore.

### Create a Keystore (First time)

```bash
keytool -genkey -v -keystore studybuddy.keystore -keyalg RSA -keysize 2048 -validity 10000 -alias studybuddy
```

This creates `studybuddy.keystore` - **KEEP THIS FILE SAFE!**

### Sign the Release AAB/APK

Edit `app/build.gradle` and add signing config:

```gradle
android {
    signingConfigs {
        release {
            storeFile file('studybuddy.keystore')
            storePassword 'your_password'
            keyAlias 'studybuddy'
            keyPassword 'your_password'
        }
    }
    
    buildTypes {
        release {
            signingConfig signingConfigs.release
            minifyEnabled false
        }
    }
}
```

Then build:

```bash
./gradlew bundleRelease
```

## Uploading to Google Play Store

1. Go to [Google Play Console](https://play.google.com/console)
2. Create a new app
3. Fill in app details
4. Go to Release → Production
5. Click "Create new release"
6. Upload the signed AAB file from `app/build/outputs/bundle/release/app-release.aab`
7. Review and submit

## Features Included

✅ WebView integration
✅ JavaScript enabled
✅ Local storage support
✅ Database support
✅ Back button navigation
✅ Internet permissions
✅ Ready for Google Play

## Troubleshooting

### White screen on app launch?
- Check your web URL is correct
- Ensure internet permission is granted
- Check logcat for errors: `adb logcat`

### App crashes?
- Clear app data: Settings → Apps → Study Buddy → Clear Cache
- Rebuild: `./gradlew clean bundleRelease`

### WebView issues?
- Update Chrome WebView from Play Store
- Check AndroidManifest.xml has internet permission

## Next Steps

1. Test on physical device or emulator
2. Create app icon (512x512 PNG)
3. Prepare screenshots for Play Store
4. Create privacy policy
5. Deploy your web app to a live server
6. Update URL in MainActivity.java
7. Build and sign the app
8. Submit to Google Play Store

## Support

For help, check:
- [Android Developers Guide](https://developer.android.com/guide)
- [WebView Documentation](https://developer.android.com/reference/android/webkit/WebView)
- [Google Play Console Help](https://support.google.com/googleplay/android-developer)
