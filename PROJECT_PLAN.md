# Recepten Verzamelaar - Project Plan

## 📋 Project Overview

**App Name:** Recepten Verzamelaar  
**Platform:** Android (Flutter)  
**Minimum SDK:** Android 6.0 (API 23)  
**Purpose:** Share TikTok recipe URLs directly to a webhook endpoint

## 🎯 User Story

A user sees a recipe on TikTok and wants to save it to their recipe book. They:
1. Tap the Share button in TikTok
2. Select "Recepten Verzamelaar" from the share menu
3. See a loading spinner while the URL is being sent
4. See a green checkmark with animation on success → app auto-closes
5. See a red cross with "Probeer Opnieuw" button on failure

## 🏗️ Technical Architecture

### Core Components

```
┌─────────────────────────────────────────┐
│         TikTok App (Share)              │
└──────────────┬──────────────────────────┘
               │ Share Intent (URL)
               ▼
┌─────────────────────────────────────────┐
│    Recepten Verzamelaar App             │
│  ┌───────────────────────────────────┐  │
│  │  Main Screen (StatefulWidget)     │  │
│  │  - Loading Spinner                │  │
│  │  - Success Animation (✓)          │  │
│  │  - Error Animation (✗)            │  │
│  │  - Retry Button                   │  │
│  └───────────────────────────────────┘  │
│  ┌───────────────────────────────────┐  │
│  │  Webhook Service                  │  │
│  │  - HTTP POST to n8n endpoint     │  │
│  │  - Error handling                 │  │
│  └───────────────────────────────────┘  │
└──────────────┬──────────────────────────┘
               │ POST {"url": "..."}
               ▼
┌─────────────────────────────────────────┐
│  https://n8n.cinematen.be/webhook/     │
│  recepten                               │
└─────────────────────────────────────────┘
```

### State Flow Diagram

```
┌─────────────┐
│   App Start │
│ (via Share) │
└──────┬──────┘
       │
       ▼
┌─────────────┐
│   LOADING   │ ◄──────────┐
│   Spinner   │            │
└──────┬──────┘            │
       │                   │
       │ HTTP POST         │
       │                   │
       ▼                   │
   ┌───────┐               │
   │Success│               │
   │  ?    │               │
   └───┬───┘               │
       │                   │
   ┌───┴───┐               │
   │       │               │
  YES     NO               │
   │       │               │
   ▼       ▼               │
┌─────┐ ┌─────┐            │
│  ✓  │ │  ✗  │            │
│Green│ │ Red │            │
└──┬──┘ └──┬──┘            │
   │       │               │
   │       ▼               │
   │  ┌────────┐           │
   │  │ Retry  │───────────┘
   │  │ Button │
   │  └────────┘
   │
   ▼
┌──────────┐
│Auto-close│
│  (2 sec) │
└──────────┘
```

## 📦 Project Structure

```
recepten-verzamelaar/
├── android/
│   ├── app/
│   │   ├── src/main/
│   │   │   ├── AndroidManifest.xml    # Share intent configuration
│   │   │   ├── res/
│   │   │   │   ├── mipmap-*/          # App icons
│   │   │   │   └── values/
│   │   │   │       └── strings.xml    # App name
│   │   └── build.gradle               # minSdkVersion 23
├── lib/
│   ├── main.dart                      # App entry point
│   ├── screens/
│   │   └── share_handler_screen.dart  # Main UI screen
│   ├── services/
│   │   └── webhook_service.dart       # HTTP service
│   ├── widgets/
│   │   ├── loading_spinner.dart       # Animated spinner
│   │   ├── success_animation.dart     # Green checkmark
│   │   └── error_animation.dart       # Red cross
│   └── constants/
│       └── app_constants.dart         # Webhook URL, colors
├── pubspec.yaml                       # Dependencies
└── README.md                          # Documentation
```

## 🔧 Technical Implementation Details

### 1. Share Intent Configuration (AndroidManifest.xml)

```xml
<intent-filter>
    <action android:name="android.intent.action.SEND" />
    <category android:name="android.intent.category.DEFAULT" />
    <data android:mimeType="text/plain" />
</intent-filter>
```

### 2. Required Flutter Packages

- `http: ^1.1.0` - HTTP requests to webhook
- `receive_sharing_intent: ^1.5.3` - Handle share intents
- `lottie: ^3.0.0` - Smooth animations (optional)
- `flutter_animate: ^4.3.0` - Simple animations (alternative)

### 3. Webhook API Specification

**Endpoint:** `https://n8n.cinematen.be/webhook/recepten`  
**Method:** POST  
**Headers:** `Content-Type: application/json`  
**Body:**
```json
{
  "url": "https://www.tiktok.com/@user/video/1234567890"
}
```

**Success Response:** HTTP 200-299  
**Error Response:** HTTP 400+ or network error

### 4. UI States & Animations

| State | Visual | Duration | Action |
|-------|--------|----------|--------|
| Loading | Circular spinner (Material Design) | Until response | - |
| Success | Green checkmark with scale animation | 1.5s | Auto-close after 2s |
| Error | Red cross with shake animation | - | Show retry button |

### 5. Color Scheme (Material Design)

- **Primary:** `Colors.blue` (Material Blue)
- **Success:** `Colors.green[600]` (#43A047)
- **Error:** `Colors.red[600]` (#E53935)
- **Background:** `Colors.white`
- **Loading:** `Colors.blue[400]`

## 📱 App Configuration

- **Package Name:** `be.cinematen.receptenverzamelaar`
- **App Name:** Recepten Verzamelaar
- **Version:** 1.0.0
- **Build Number:** 1

## 🧪 Testing Strategy

1. **Unit Tests:**
   - Webhook service HTTP calls
   - URL validation
   - Error handling logic

2. **Integration Tests:**
   - Share intent reception
   - Full flow from share to webhook
   - Auto-close timing

3. **Manual Testing:**
   - Share from TikTok app
   - Test with various URL formats
   - Network error scenarios
   - Success flow end-to-end

## 🚀 Deployment Steps

1. Build release APK: `flutter build apk --release`
2. Test on physical Android device
3. Optional: Sign APK for distribution
4. Optional: Publish to Google Play Store (future)

## 📝 Development Phases

### Phase 1: Repository & Project Setup
- Create GitHub repository
- Initialize Flutter project
- Configure Android settings

### Phase 2: Core Functionality
- Implement share intent handling
- Create webhook service
- Build main UI screen

### Phase 3: UI/UX Polish
- Add animations
- Design app icon
- Implement auto-close

### Phase 4: Testing & Refinement
- Test with TikTok
- Handle edge cases
- Performance optimization

### Phase 5: Documentation & Deployment
- Write README
- Build release APK
- Create installation guide

## 🔒 Security Considerations

- No sensitive data stored locally
- HTTPS endpoint (n8n.cinematen.be)
- No authentication required (as specified)
- URL validation before sending

## 🎨 App Icon Design Ideas

- Recipe book icon
- Fork and knife crossed
- TikTok-style play button with recipe book
- Simple cookbook with bookmark

## 📚 Documentation Requirements

README should include:
- App description and purpose
- Installation instructions
- How to use (share from TikTok)
- Troubleshooting guide
- Development setup for contributors

## ⚠️ Known Limitations & Future Enhancements

**Current Limitations:**
- Android only (no iOS)
- TikTok URLs only (can be extended)
- No offline queue (requires internet)

**Future Enhancements:**
- iOS support
- Support for other platforms (Instagram, YouTube)
- Offline queue with retry mechanism
- User settings (custom webhook URL)
- Share history/log