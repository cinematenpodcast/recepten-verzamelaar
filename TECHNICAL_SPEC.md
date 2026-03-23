# Technical Specification - Recepten Verzamelaar

## 🔧 Implementation Details

### 1. AndroidManifest.xml Configuration

**Location:** `android/app/src/main/AndroidManifest.xml`

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android">
    <!-- Internet permission for webhook calls -->
    <uses-permission android:name="android.permission.INTERNET"/>
    
    <application
        android:label="Recepten Verzamelaar"
        android:name="${applicationName}"
        android:icon="@mipmap/ic_launcher">
        
        <activity
            android:name=".MainActivity"
            android:exported="true"
            android:launchMode="singleTop"
            android:theme="@style/LaunchTheme"
            android:configChanges="orientation|keyboardHidden|keyboard|screenSize|smallestScreenSize|locale|layoutDirection|fontScale|screenLayout|density|uiMode"
            android:hardwareAccelerated="true"
            android:windowSoftInputMode="adjustResize">
            
            <!-- Standard launcher intent -->
            <intent-filter>
                <action android:name="android.intent.action.MAIN"/>
                <category android:name="android.intent.category.LAUNCHER"/>
            </intent-filter>
            
            <!-- Share intent filter for receiving URLs -->
            <intent-filter>
                <action android:name="android.intent.action.SEND" />
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/plain" />
            </intent-filter>
        </activity>
    </application>
</manifest>
```

### 2. build.gradle Configuration

**Location:** `android/app/build.gradle`

```gradle
android {
    compileSdkVersion 34
    
    defaultConfig {
        applicationId "be.cinematen.receptenverzamelaar"
        minSdkVersion 23  // Android 6.0
        targetSdkVersion 34
        versionCode 1
        versionName "1.0.0"
    }
}
```

### 3. pubspec.yaml Dependencies

```yaml
name: recepten_verzamelaar
description: Share TikTok recipes to your recipe book
version: 1.0.0+1

environment:
  sdk: '>=3.0.0 <4.0.0'

dependencies:
  flutter:
    sdk: flutter
  
  # HTTP requests
  http: ^1.1.0
  
  # Handle share intents
  receive_sharing_intent: ^1.5.3
  
  # Animations
  flutter_animate: ^4.3.0
  
  # System services
  flutter_native_splash: ^2.3.5

dev_dependencies:
  flutter_test:
    sdk: flutter
  flutter_lints: ^3.0.0

flutter:
  uses-material-design: true
  
  # App icon configuration
  assets:
    - assets/images/
```

### 4. Main Application Entry Point

**File:** `lib/main.dart`

```dart
import 'package:flutter/material.dart';
import 'package:receive_sharing_intent/receive_sharing_intent.dart';
import 'screens/share_handler_screen.dart';
import 'constants/app_constants.dart';

void main() {
  runApp(const ReceptenVerzamelaarApp());
}

class ReceptenVerzamelaarApp extends StatelessWidget {
  const ReceptenVerzamelaarApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Recepten Verzamelaar',
      theme: ThemeData(
        primarySwatch: Colors.blue,
        useMaterial3: true,
      ),
      home: const ShareHandlerScreen(),
      debugShowCheckedModeBanner: false,
    );
  }
}
```

### 5. Constants Configuration

**File:** `lib/constants/app_constants.dart`

```dart
class AppConstants {
  // Webhook configuration
  static const String webhookUrl = 'https://n8n.cinematen.be/webhook/recepten';
  static const Duration requestTimeout = Duration(seconds: 10);
  
  // UI timing
  static const Duration successDisplayDuration = Duration(milliseconds: 1500);
  static const Duration autoCloseDuration = Duration(seconds: 2);
  
  // Colors
  static const Color successColor = Color(0xFF43A047); // Green 600
  static const Color errorColor = Color(0xFFE53935);   // Red 600
  static const Color loadingColor = Color(0xFF42A5F5); // Blue 400
  
  // Animation durations
  static const Duration spinnerDuration = Duration(milliseconds: 1000);
  static const Duration checkmarkDuration = Duration(milliseconds: 600);
  static const Duration crossDuration = Duration(milliseconds: 400);
}
```

### 6. Webhook Service

**File:** `lib/services/webhook_service.dart`

```dart
import 'dart:convert';
import 'package:http/http.dart' as http;
import '../constants/app_constants.dart';

class WebhookService {
  Future<bool> sendUrlToWebhook(String url) async {
    try {
      final response = await http
          .post(
            Uri.parse(AppConstants.webhookUrl),
            headers: {
              'Content-Type': 'application/json',
            },
            body: jsonEncode({
              'url': url,
            }),
          )
          .timeout(AppConstants.requestTimeout);

      // Success if status code is 2xx
      return response.statusCode >= 200 && response.statusCode < 300;
    } catch (e) {
      // Network error, timeout, or other exception
      print('Webhook error: $e');
      return false;
    }
  }
}
```

### 7. Main Share Handler Screen

**File:** `lib/screens/share_handler_screen.dart`

```dart
import 'package:flutter/material.dart';
import 'package:flutter/services.dart';
import 'package:receive_sharing_intent/receive_sharing_intent.dart';
import 'dart:async';
import '../services/webhook_service.dart';
import '../constants/app_constants.dart';
import '../widgets/loading_spinner.dart';
import '../widgets/success_animation.dart';
import '../widgets/error_animation.dart';

enum ShareState { loading, success, error }

class ShareHandlerScreen extends StatefulWidget {
  const ShareHandlerScreen({super.key});

  @override
  State<ShareHandlerScreen> createState() => _ShareHandlerScreenState();
}

class _ShareHandlerScreenState extends State<ShareHandlerScreen> {
  ShareState _currentState = ShareState.loading;
  String? _sharedUrl;
  final WebhookService _webhookService = WebhookService();
  StreamSubscription? _intentSubscription;

  @override
  void initState() {
    super.initState();
    _initializeSharing();
  }

  void _initializeSharing() {
    // Handle initial shared data when app is opened via share
    ReceiveSharingIntent.getInitialText().then((String? value) {
      if (value != null) {
        setState(() {
          _sharedUrl = value;
        });
        _sendToWebhook(value);
      }
    });

    // Handle shared data while app is running
    _intentSubscription = ReceiveSharingIntent.getTextStream().listen(
      (String value) {
        setState(() {
          _sharedUrl = value;
          _currentState = ShareState.loading;
        });
        _sendToWebhook(value);
      },
      onError: (err) {
        setState(() {
          _currentState = ShareState.error;
        });
      },
    );
  }

  Future<void> _sendToWebhook(String url) async {
    setState(() {
      _currentState = ShareState.loading;
    });

    final success = await _webhookService.sendUrlToWebhook(url);

    if (!mounted) return;

    setState(() {
      _currentState = success ? ShareState.success : ShareState.error;
    });

    if (success) {
      // Auto-close after showing success animation
      await Future.delayed(AppConstants.autoCloseDuration);
      if (mounted) {
        SystemNavigator.pop(); // Close the app
      }
    }
  }

  void _retry() {
    if (_sharedUrl != null) {
      _sendToWebhook(_sharedUrl!);
    }
  }

  @override
  void dispose() {
    _intentSubscription?.cancel();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Colors.white,
      body: SafeArea(
        child: Center(
          child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            children: [
              _buildStateWidget(),
              if (_currentState == ShareState.error) ...[
                const SizedBox(height: 32),
                ElevatedButton.icon(
                  onPressed: _retry,
                  icon: const Icon(Icons.refresh),
                  label: const Text('Probeer Opnieuw'),
                  style: ElevatedButton.styleFrom(
                    backgroundColor: AppConstants.errorColor,
                    foregroundColor: Colors.white,
                    padding: const EdgeInsets.symmetric(
                      horizontal: 24,
                      vertical: 12,
                    ),
                  ),
                ),
              ],
            ],
          ),
        ),
      ),
    );
  }

  Widget _buildStateWidget() {
    switch (_currentState) {
      case ShareState.loading:
        return const LoadingSpinner();
      case ShareState.success:
        return const SuccessAnimation();
      case ShareState.error:
        return const ErrorAnimation();
    }
  }
}
```

### 8. Loading Spinner Widget

**File:** `lib/widgets/loading_spinner.dart`

```dart
import 'package:flutter/material.dart';
import '../constants/app_constants.dart';

class LoadingSpinner extends StatelessWidget {
  const LoadingSpinner({super.key});

  @override
  Widget build(BuildContext context) {
    return Column(
      mainAxisSize: MainAxisSize.min,
      children: [
        SizedBox(
          width: 80,
          height: 80,
          child: CircularProgressIndicator(
            strokeWidth: 6,
            valueColor: AlwaysStoppedAnimation<Color>(
              AppConstants.loadingColor,
            ),
          ),
        ),
        const SizedBox(height: 24),
        const Text(
          'Recept opslaan...',
          style: TextStyle(
            fontSize: 18,
            fontWeight: FontWeight.w500,
            color: Colors.black87,
          ),
        ),
      ],
    );
  }
}
```

### 9. Success Animation Widget

**File:** `lib/widgets/success_animation.dart`

```dart
import 'package:flutter/material.dart';
import 'package:flutter_animate/flutter_animate.dart';
import '../constants/app_constants.dart';

class SuccessAnimation extends StatelessWidget {
  const SuccessAnimation({super.key});

  @override
  Widget build(BuildContext context) {
    return Column(
      mainAxisSize: MainAxisSize.min,
      children: [
        Container(
          width: 100,
          height: 100,
          decoration: BoxDecoration(
            color: AppConstants.successColor,
            shape: BoxShape.circle,
          ),
          child: const Icon(
            Icons.check,
            color: Colors.white,
            size: 60,
          ),
        )
            .animate()
            .scale(
              duration: AppConstants.checkmarkDuration,
              curve: Curves.elasticOut,
            )
            .fadeIn(),
        const SizedBox(height: 24),
        const Text(
          'Recept opgeslagen!',
          style: TextStyle(
            fontSize: 18,
            fontWeight: FontWeight.w600,
            color: Colors.black87,
          ),
        ).animate().fadeIn(delay: 200.ms),
      ],
    );
  }
}
```

### 10. Error Animation Widget

**File:** `lib/widgets/error_animation.dart`

```dart
import 'package:flutter/material.dart';
import 'package:flutter_animate/flutter_animate.dart';
import '../constants/app_constants.dart';

class ErrorAnimation extends StatelessWidget {
  const ErrorAnimation({super.key});

  @override
  Widget build(BuildContext context) {
    return Column(
      mainAxisSize: MainAxisSize.min,
      children: [
        Container(
          width: 100,
          height: 100,
          decoration: BoxDecoration(
            color: AppConstants.errorColor,
            shape: BoxShape.circle,
          ),
          child: const Icon(
            Icons.close,
            color: Colors.white,
            size: 60,
          ),
        )
            .animate()
            .shake(
              duration: AppConstants.crossDuration,
              hz: 4,
            )
            .fadeIn(),
        const SizedBox(height: 24),
        const Text(
          'Er ging iets mis',
          style: TextStyle(
            fontSize: 18,
            fontWeight: FontWeight.w600,
            color: Colors.black87,
          ),
        ).animate().fadeIn(delay: 200.ms),
      ],
    );
  }
}
```

## 🧪 Testing Examples

### Unit Test for Webhook Service

**File:** `test/services/webhook_service_test.dart`

```dart
import 'package:flutter_test/flutter_test.dart';
import 'package:recepten_verzamelaar/services/webhook_service.dart';

void main() {
  group('WebhookService', () {
    late WebhookService service;

    setUp(() {
      service = WebhookService();
    });

    test('should handle valid TikTok URL', () async {
      const testUrl = 'https://www.tiktok.com/@user/video/1234567890';
      
      // Note: This will make a real HTTP call in integration tests
      // For unit tests, you would mock the http client
      final result = await service.sendUrlToWebhook(testUrl);
      
      expect(result, isA<bool>());
    });

    test('should handle network errors gracefully', () async {
      const invalidUrl = 'not-a-valid-url';
      
      final result = await service.sendUrlToWebhook(invalidUrl);
      
      // Should return false on error, not throw
      expect(result, isFalse);
    });
  });
}
```

## 📱 Build Commands

### Development Build
```bash
flutter run
```

### Release APK
```bash
flutter build apk --release
```

### Release App Bundle (for Play Store)
```bash
flutter build appbundle --release
```

### Install on Device
```bash
flutter install
```

## 🔍 Debugging

### View Logs
```bash
flutter logs
```

### Check Share Intent
```bash
adb logcat | grep -i "intent"
```

### Test Webhook Manually
```bash
curl -X POST https://n8n.cinematen.be/webhook/recepten \
  -H "Content-Type: application/json" \
  -d '{"url":"https://www.tiktok.com/@test/video/123"}'
```

## 🎨 App Icon Generation

Use a tool like:
- [App Icon Generator](https://appicon.co/)
- [Icon Kitchen](https://icon.kitchen/)

Required sizes for Android:
- mdpi: 48x48
- hdpi: 72x72
- xhdpi: 96x96
- xxhdpi: 144x144
- xxxhdpi: 192x192

## 📋 Pre-launch Checklist

- [ ] Test share from TikTok app
- [ ] Verify webhook receives correct data
- [ ] Test success flow (green checkmark + auto-close)
- [ ] Test error flow (red cross + retry button)
- [ ] Test network timeout scenarios
- [ ] Verify app icon displays correctly
- [ ] Test on multiple Android versions (6.0+)
- [ ] Check app permissions in settings
- [ ] Verify app name in launcher
- [ ] Test retry functionality works correctly