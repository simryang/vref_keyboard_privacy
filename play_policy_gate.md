# Vref Keyboard - Google Play Policy Gate

Last reviewed: 2026-05-07

This document is a release-readiness checklist for Google Play submission. It is not legal advice. Before production release, re-check the current Google Play Developer Program Policies and Play Console warnings.

## Official References

- Target API requirement: https://developer.android.com/google/play/requirements/target-sdk
- User Data policy: https://support.google.com/googleplay/android-developer/answer/10144311
- Data safety section: https://support.google.com/googleplay/android-developer/answer/10787469
- Sensitive permissions policy: https://support.google.com/googleplay/android-developer/answer/16558241
- Prominent disclosure and consent: https://support.google.com/googleplay/android-developer/answer/11150561

## Current App Facts

- Package: `com.ahavasoft.vrefkeyboard`
- App type: Android input method / keyboard
- Current SDK config: `compileSdk = 34`, `targetSdk = 34`, `minSdk = 26`
- Permissions:
  - `android.permission.VIBRATE`
  - `android.permission.INTERNET`
  - `android.permission.RECORD_AUDIO`
  - `com.android.vending.BILLING`
- Third-party / platform services:
  - Firebase Crashlytics
  - Google Play Billing
  - Android `SpeechRecognizer` for voice input
- Core privacy claim:
  - The app does not store, upload, sell, or share typed keyboard input.
  - Voice input is user-initiated. Audio is handled by the Android speech recognition service available on the device; Vref Keyboard receives recognized text and commits it to the current input field.

## Release Gate Checklist

| Gate | Current status | Required action before Play production |
| --- | --- | --- |
| Target API | Blocker: app targets API 34 | Upgrade `compileSdk` and `targetSdk` to API 35 or higher, then test Android 15 behavior changes. |
| Billing Library | Likely blocker/risk: `billing-ktx:6.1.0` | Upgrade to a currently accepted Play Billing Library version and re-test purchase, restore, and acknowledge flows. |
| Release artifact | Not yet confirmed | Build and upload an Android App Bundle: `./gradlew :app:bundleRelease`. |
| Release signing | Exists locally but must be verified | Verify Play App Signing setup, release keystore, `keystore.properties`, and `versionCode` increment. |
| Privacy policy URL | Missing | Publish `docs/privacy_policy_ko.md` content as a public, non-PDF, non-editable webpage. Add the URL in Play Console and in-app. |
| Data safety | Missing | Complete Play Console Data safety form consistently with privacy policy. |
| Sensitive permission: microphone | Needs clear disclosure | Store listing and in-app voice permission flow must explain voice input. Request permission only when user taps voice input. |
| Keyboard input sensitivity | High trust area | State clearly: no typed text collection, no keystroke analytics, no server upload of input text. |
| Crash reporting | Needs disclosure | Disclose Crashlytics collection of crash diagnostics, app/device state, and identifiers used for crash grouping. |
| In-app purchase | Needs disclosure | Disclose Google Play Billing use and local storage of purchase token/status for Pro entitlement. |
| Children / Families | Likely avoid | Unless intentionally targeting children, set target audience to general / not child-directed. Do not claim Families eligibility without review. |
| Ads | No ads currently | If no ads SDK exists, declare no ads. Revisit if ads are added. |
| Testing track | Account-dependent | For new personal developer accounts, follow Play Console testing requirements before production access. |
| App content | Pending | Complete content rating, target audience, data safety, ads, financial features, and app access forms. |
| Technical QA | Pending | Test fixed and floating modes across portrait, landscape, Samsung keyboard switch, reboot, app updates, voice permission deny/allow, purchase restore, and crash-free launch. |

## Recommended Data Safety Answers

These are draft answers. Confirm against the final SDK versions and Play Console's SDK Index before submission.

### Data collected

Likely `Yes` for:

- App info and performance:
  - Crash logs
  - Diagnostics
  - Other app performance data
- Device or other IDs:
  - Used by Crashlytics / Google Play services for diagnostics and purchase infrastructure
- Financial info / purchase history:
  - Google Play Billing processes purchases. The app receives purchase state and purchase token for entitlement.

Likely `No` for:

- Typed text / keyboard input collection by Vref Keyboard servers
- Contacts
- Location
- Photos / videos
- Files and docs
- SMS / call logs
- Health data

### Data sharing

Likely `Yes` for service providers only:

- Firebase Crashlytics receives crash diagnostics.
- Google Play Billing processes purchase-related information.
- Android speech recognition service may process voice audio when the user starts voice input.

No sale of personal or sensitive user data.

### Security practices

- Data is transmitted using modern transport security by Google/Firebase/Play services.
- Purchase status and tokens are stored locally using app-private storage / encrypted preferences where implemented.
- Users can request deletion of locally stored app data by clearing app data or uninstalling the app. For support requests, provide a contact email.

## Store Listing Privacy Copy

Short version for store listing:

> Vref Keyboard is designed to keep typed input private. The keyboard does not collect, store, upload, sell, or share what you type. Optional voice input uses Android speech recognition only when you tap the voice button. Crash reports may be sent through Firebase Crashlytics to help diagnose app stability issues.

Permission explanation:

> Microphone access is used only for the optional voice input feature. Internet access is used for crash reporting, Google Play Billing, and Google platform services; it is not used to upload typed keyboard input.

## Prominent Disclosure Draft for Voice Input

Show before requesting microphone permission or near the voice input button:

> Voice input uses your microphone and the Android speech recognition service to convert speech to text. Vref Keyboard receives the recognized text and inserts it into the current text field. Vref Keyboard does not store or upload your typed keyboard input.

Actions:

- Button: `Continue`
- Button: `Cancel`

## Pre-submit Verification

- [ ] `targetSdk >= 35`
- [ ] Billing Library upgraded and purchase flow tested
- [ ] `bundleRelease` builds
- [ ] Release AAB uploaded to internal testing
- [ ] Privacy policy URL is public and non-PDF
- [ ] Privacy policy link exists in Play Console
- [ ] Privacy policy link/text exists in app
- [ ] Data safety answers match the privacy policy
- [ ] Microphone permission is requested only after user taps voice input
- [ ] Store listing does not imply cloud sync, AI input collection, or account features unless actually implemented
- [ ] No debug-only Crashlytics test crash flag is enabled
- [ ] No test APK/debug build uploaded to production
