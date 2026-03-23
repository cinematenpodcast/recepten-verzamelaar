# GitHub Issues - Recepten Verzamelaar

Deze issues moeten aangemaakt worden in de GitHub repository om het project stap voor stap te implementeren.

---

## Issue #1: Project Setup & Repository Configuration

**Labels:** `setup`, `documentation`  
**Milestone:** v1.0.0  
**Assignee:** TBD

### Description
Initialize the Flutter project and configure the repository with proper documentation and structure.

### Tasks
- [ ] Create Flutter project with `flutter create recepten_verzamelaar`
- [ ] Configure `pubspec.yaml` with required dependencies
- [ ] Set up `.gitignore` for Flutter/Android
- [ ] Create initial README.md from template
- [ ] Add LICENSE file (MIT)
- [ ] Create project structure (lib/screens, lib/services, lib/widgets, lib/constants)
- [ ] Configure Android package name: `be.cinematen.receptenverzamelaar`

### Acceptance Criteria
- Flutter project runs successfully with `flutter run`
- All dependencies install without errors
- Project structure follows Flutter best practices
- README contains basic project information

---

## Issue #2: Android Configuration & Share Intent Setup

**Labels:** `android`, `configuration`  
**Milestone:** v1.0.0  
**Assignee:** TBD

### Description
Configure Android-specific settings to enable the app to receive share intents from other apps like TikTok.

### Tasks
- [ ] Update `android/app/build.gradle` with minSdkVersion 23
- [ ] Configure `AndroidManifest.xml` with SEND intent filter
- [ ] Add Internet permission to manifest
- [ ] Set app name to "Recepten Verzamelaar"
- [ ] Configure package identifier: `be.cinematen.receptenverzamelaar`
- [ ] Test that app appears in share menu

### Technical Details
```xml
<intent-filter>
    <action android:name="android.intent.action.SEND" />
    <category android:name="android.intent.category.DEFAULT" />
    <data android:mimeType="text/plain" />
</intent-filter>
```

### Acceptance Criteria
- App appears in Android share menu
- App can receive text/URL from share intent
- Minimum SDK version is set to 23 (Android 6.0)
- Internet permission is granted

---

## Issue #3: Implement Webhook Service

**Labels:** `backend`, `service`  
**Milestone:** v1.0.0  
**Assignee:** TBD

### Description
Create a service class to handle HTTP POST requests to the n8n webhook endpoint.

### Tasks
- [ ] Create `lib/services/webhook_service.dart`
- [ ] Implement POST request to `https://n8n.cinematen.be/webhook/recepten`
- [ ] Add proper error handling for network failures
- [ ] Add timeout configuration (10 seconds)
- [ ] Return boolean for success/failure
- [ ] Add logging for debugging

### API Specification
- **Endpoint:** `https://n8n.cinematen.be/webhook/recepten`
- **Method:** POST
- **Headers:** `Content-Type: application/json`
- **Body:** `{"url": "shared_url_here"}`
- **Success:** HTTP 200-299
- **Error:** HTTP 400+ or network error

### Acceptance Criteria
- Service successfully sends URL to webhook
- Handles network errors gracefully
- Returns clear success/failure status
- Includes proper timeout handling

---

## Issue #4: Create Constants Configuration

**Labels:** `configuration`  
**Milestone:** v1.0.0  
**Assignee:** TBD

### Description
Set up a constants file for all configuration values used throughout the app.

### Tasks
- [ ] Create `lib/constants/app_constants.dart`
- [ ] Define webhook URL constant
- [ ] Define timeout durations
- [ ] Define UI colors (success green, error red, loading blue)
- [ ] Define animation durations
- [ ] Define auto-close timing

### Constants to Include
- Webhook URL
- Request timeout (10s)
- Success display duration (1.5s)
- Auto-close duration (2s)
- Material Design colors

### Acceptance Criteria
- All magic numbers/strings are in constants file
- Colors follow Material Design guidelines
- Durations are configurable
- Constants are properly typed

---

## Issue #5: Build Loading Spinner Widget

**Labels:** `ui`, `widget`  
**Milestone:** v1.0.0  
**Assignee:** TBD

### Description
Create an animated loading spinner widget that displays while the webhook request is in progress.

### Tasks
- [ ] Create `lib/widgets/loading_spinner.dart`
- [ ] Implement Material Design circular progress indicator
- [ ] Add "Recept opslaan..." text below spinner
- [ ] Use blue color from constants
- [ ] Make spinner size 80x80
- [ ] Center widget on screen

### Design Specs
- Spinner diameter: 80px
- Stroke width: 6px
- Color: Material Blue 400
- Text: "Recept opslaan..."
- Font size: 18sp

### Acceptance Criteria
- Spinner animates smoothly
- Text is clearly visible
- Widget is properly centered
- Uses colors from constants

---

## Issue #6: Build Success Animation Widget

**Labels:** `ui`, `widget`, `animation`  
**Milestone:** v1.0.0  
**Assignee:** TBD

### Description
Create an animated success widget with a green checkmark that appears when the webhook call succeeds.

### Tasks
- [ ] Create `lib/widgets/success_animation.dart`
- [ ] Implement circular green background
- [ ] Add white checkmark icon
- [ ] Add scale animation with elastic curve
- [ ] Add fade-in animation
- [ ] Add "Recept opgeslagen!" text
- [ ] Use flutter_animate package

### Design Specs
- Circle diameter: 100px
- Background: Material Green 600
- Icon: White checkmark, size 60
- Animation: Scale with elastic out curve (600ms)
- Text: "Recept opgeslagen!"

### Acceptance Criteria
- Checkmark animates smoothly with bounce effect
- Green color matches Material Design
- Animation timing feels natural
- Text appears with fade-in

---

## Issue #7: Build Error Animation Widget

**Labels:** `ui`, `widget`, `animation`  
**Milestone:** v1.0.0  
**Assignee:** TBD

### Description
Create an animated error widget with a red cross that appears when the webhook call fails.

### Tasks
- [ ] Create `lib/widgets/error_animation.dart`
- [ ] Implement circular red background
- [ ] Add white cross (X) icon
- [ ] Add shake animation
- [ ] Add fade-in animation
- [ ] Add "Er ging iets mis" text
- [ ] Use flutter_animate package

### Design Specs
- Circle diameter: 100px
- Background: Material Red 600
- Icon: White X, size 60
- Animation: Shake (400ms, 4Hz)
- Text: "Er ging iets mis"

### Acceptance Criteria
- Cross animates with shake effect
- Red color matches Material Design
- Animation feels responsive
- Text is clear and visible

---

## Issue #8: Implement Main Share Handler Screen

**Labels:** `ui`, `screen`, `core`  
**Milestone:** v1.0.0  
**Assignee:** TBD

### Description
Create the main screen that handles share intents and orchestrates the entire flow.

### Tasks
- [ ] Create `lib/screens/share_handler_screen.dart`
- [ ] Implement state management (loading, success, error)
- [ ] Handle initial share intent with `receive_sharing_intent`
- [ ] Handle share intent stream for running app
- [ ] Call webhook service with shared URL
- [ ] Display appropriate widget based on state
- [ ] Implement auto-close after success
- [ ] Add retry button for error state

### State Flow
1. **Loading** → Show spinner, call webhook
2. **Success** → Show checkmark, wait 2s, close app
3. **Error** → Show cross, show retry button

### Acceptance Criteria
- Receives shared URLs correctly
- State transitions work smoothly
- Auto-close works after success
- Retry button functions properly
- App closes using SystemNavigator.pop()

---

## Issue #9: Implement Retry Functionality

**Labels:** `feature`, `error-handling`  
**Milestone:** v1.0.0  
**Assignee:** TBD

### Description
Add a retry button that appears on error and allows users to resend the URL to the webhook.

### Tasks
- [ ] Add "Probeer Opnieuw" button to error state
- [ ] Style button with red background
- [ ] Add refresh icon to button
- [ ] Reset state to loading on retry
- [ ] Resend URL to webhook
- [ ] Handle multiple retry attempts

### Design Specs
- Button text: "Probeer Opnieuw"
- Background: Material Red 600
- Foreground: White
- Icon: Refresh icon
- Padding: 24px horizontal, 12px vertical

### Acceptance Criteria
- Button appears only on error state
- Clicking button retries webhook call
- State resets to loading properly
- Multiple retries work correctly

---

## Issue #10: Design and Implement App Icon

**Labels:** `design`, `ui`, `assets`  
**Milestone:** v1.0.0  
**Assignee:** TBD

### Description
Create a custom app icon with a recipe/cookbook theme for the Android launcher.

### Tasks
- [ ] Design app icon concept (cookbook, fork/knife, or recipe theme)
- [ ] Create icon in required Android sizes:
  - mdpi: 48x48
  - hdpi: 72x72
  - xhdpi: 96x96
  - xxhdpi: 144x144
  - xxxhdpi: 192x192
- [ ] Place icons in `android/app/src/main/res/mipmap-*` folders
- [ ] Update `AndroidManifest.xml` to reference icon
- [ ] Test icon appears correctly in launcher

### Design Guidelines
- Simple and recognizable
- Works well at small sizes
- Recipe/cookbook theme
- Matches app purpose

### Acceptance Criteria
- Icon displays in Android launcher
- Icon looks good at all sizes
- Icon is recognizable and on-brand
- All required sizes are included

---

## Issue #11: Add Auto-Close Functionality

**Labels:** `feature`, `ux`  
**Milestone:** v1.0.0  
**Assignee:** TBD

### Description
Implement automatic app closure after successful webhook submission to provide seamless UX.

### Tasks
- [ ] Add 2-second delay after success animation
- [ ] Use `SystemNavigator.pop()` to close app
- [ ] Ensure delay is configurable via constants
- [ ] Test that user returns to TikTok app
- [ ] Handle edge cases (app in background, etc.)

### Technical Details
```dart
await Future.delayed(AppConstants.autoCloseDuration);
if (mounted) {
  SystemNavigator.pop();
}
```

### Acceptance Criteria
- App closes automatically after success
- Timing feels natural (not too fast/slow)
- User returns to previous app (TikTok)
- No crashes or errors on close

---

## Issue #12: Testing & Quality Assurance

**Labels:** `testing`, `qa`  
**Milestone:** v1.0.0  
**Assignee:** TBD

### Description
Comprehensive testing of all app functionality on real Android devices.

### Test Cases
- [ ] Share from TikTok app works
- [ ] Loading spinner displays correctly
- [ ] Success flow: checkmark → auto-close
- [ ] Error flow: cross → retry button
- [ ] Retry button works correctly
- [ ] App appears in share menu
- [ ] Webhook receives correct data
- [ ] Network timeout handling
- [ ] No internet connection handling
- [ ] Multiple rapid shares
- [ ] App icon displays correctly
- [ ] Test on Android 6.0 (minimum)
- [ ] Test on Android 13+ (latest)

### Devices to Test
- Android 6.0 device
- Android 10+ device
- Android 13+ device

### Acceptance Criteria
- All test cases pass
- No crashes or errors
- Smooth user experience
- Works on minimum SDK version

---

## Issue #13: Documentation & README

**Labels:** `documentation`  
**Milestone:** v1.0.0  
**Assignee:** TBD

### Description
Complete the README with installation instructions, usage guide, and troubleshooting.

### Tasks
- [ ] Add installation instructions
- [ ] Add usage guide with screenshots
- [ ] Add troubleshooting section
- [ ] Add development setup guide
- [ ] Add build instructions
- [ ] Add contribution guidelines
- [ ] Add license information
- [ ] Add changelog

### Sections to Include
- Features
- Requirements
- Installation (APK download + build from source)
- Usage guide
- Troubleshooting
- Technical details
- Development setup
- Contributing
- License

### Acceptance Criteria
- README is complete and clear
- Instructions are easy to follow
- Screenshots show key features
- Troubleshooting covers common issues

---

## Issue #14: Build Release APK

**Labels:** `release`, `build`  
**Milestone:** v1.0.0  
**Assignee:** TBD

### Description
Build and prepare the release APK for distribution.

### Tasks
- [ ] Run `flutter build apk --release`
- [ ] Test release APK on physical device
- [ ] Verify app size is reasonable
- [ ] Check all features work in release mode
- [ ] Create GitHub release
- [ ] Upload APK to releases
- [ ] Add release notes
- [ ] Tag version as v1.0.0

### Build Commands
```bash
flutter clean
flutter pub get
flutter build apk --release
```

### Acceptance Criteria
- Release APK builds successfully
- APK size is under 20MB
- All features work in release mode
- APK is uploaded to GitHub releases
- Release notes are clear and complete

---

## Future Enhancements (Post v1.0.0)

### Issue #15: iOS Support
**Labels:** `enhancement`, `ios`  
Add iOS support for sharing from TikTok on iPhone.

### Issue #16: Multi-Platform Support
**Labels:** `enhancement`, `feature`  
Support sharing from Instagram, YouTube, and other platforms.

### Issue #17: Offline Queue
**Labels:** `enhancement`, `feature`  
Queue URLs when offline and send when connection is restored.

### Issue #18: Custom Webhook URL
**Labels:** `enhancement`, `settings`  
Allow users to configure their own webhook URL in settings.

### Issue #19: Share History
**Labels:** `enhancement`, `feature`  
Show history of shared recipes with timestamps.