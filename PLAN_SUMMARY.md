# 📱 Recepten Verzamelaar - Plan Samenvatting

## 🎯 Project Doel

Een simpele Android Flutter app die TikTok recept URLs doorsturt naar een n8n webhook endpoint (`https://n8n.cinematen.be/webhook/recepten`).

## 👤 User Story

1. Gebruiker ziet recept op TikTok
2. Drukt op Share knop
3. Selecteert "Recepten Verzamelaar" app
4. Ziet loading spinner → groen vinkje bij success → app sluit automatisch
5. Bij error: rood kruis + "Probeer Opnieuw" knop

## 📚 Planning Documenten

| Document | Beschrijving | Gebruik Voor |
|----------|--------------|--------------|
| **PROJECT_PLAN.md** | High-level overzicht, architectuur, technische details | Begrip van het grote geheel |
| **TECHNICAL_SPEC.md** | Gedetailleerde code voorbeelden en implementatie specs | Tijdens development als referentie |
| **README_TEMPLATE.md** | Template voor GitHub repository README | Kopiëren naar nieuwe repository |
| **GITHUB_ISSUES.md** | 14 voorgedefinieerde issues met tasks | Project management en tracking |
| **IMPLEMENTATION_GUIDE.md** | Stap-voor-stap implementatie gids | Dagelijkse workflow en voortgang |

## 🏗️ Technische Architectuur

```
┌─────────────────┐
│   TikTok App    │
└────────┬────────┘
         │ Share Intent (URL)
         ▼
┌─────────────────────────────────┐
│  Recepten Verzamelaar App       │
│  ┌──────────────────────────┐   │
│  │  Loading → Success/Error │   │
│  └──────────────────────────┘   │
│  ┌──────────────────────────┐   │
│  │  Webhook Service (HTTP)  │   │
│  └──────────────────────────┘   │
└────────┬────────────────────────┘
         │ POST {"url": "..."}
         ▼
┌─────────────────────────────────┐
│  n8n Webhook Endpoint           │
│  https://n8n.cinematen.be/      │
│  webhook/recepten               │
└─────────────────────────────────┘
```

## 📋 Implementatie Fases

### Phase 1: Foundation (2-3 uur)
- Issue #1: Project Setup
- Issue #2: Android Configuration
- Issue #3: Webhook Service
- Issue #4: Constants

### Phase 2: UI Components (2-3 uur)
- Issue #5: Loading Spinner
- Issue #6: Success Animation
- Issue #7: Error Animation

### Phase 3: Core Logic (3-4 uur)
- Issue #8: Main Screen
- Issue #9: Retry Functionality

### Phase 4: Polish (2-3 uur)
- Issue #10: App Icon
- Issue #11: Auto-Close

### Phase 5: Testing & Release (3-4 uur)
- Issue #12: Testing
- Issue #13: Documentation
- Issue #14: Release Build

**Totale geschatte tijd:** 12-17 uur

## 🚀 Volgende Stappen

### Stap 1: Repository Aanmaken (PM Mode)
```
Gebruik PM mode om een nieuwe GitHub repository 'recepten-verzamelaar' aan te maken
met README_TEMPLATE.md, Flutter .gitignore, en MIT license.
```

### Stap 2: Issues Aanmaken (PM Mode)
```
Maak alle 14 issues aan uit GITHUB_ISSUES.md in de nieuwe repository.
Gebruik milestone "v1.0.0" voor alle issues.
```

### Stap 3: Implementatie Starten (Code/Advanced Mode)
```
Start met Issue #1: Project Setup & Repository Configuration
Werk issues af in volgorde zoals beschreven in IMPLEMENTATION_GUIDE.md
```

## 🔧 Tech Stack

- **Framework:** Flutter 3.x
- **Language:** Dart
- **Platform:** Android (minSdkVersion 23 / Android 6.0+)
- **Key Dependencies:**
  - `http` - HTTP requests
  - `receive_sharing_intent` - Share intent handling
  - `flutter_animate` - Animations

## 📦 Project Structuur

```
recepten-verzamelaar/
├── android/
│   └── app/
│       └── src/main/
│           ├── AndroidManifest.xml
│           └── res/mipmap-*/
├── lib/
│   ├── main.dart
│   ├── screens/
│   │   └── share_handler_screen.dart
│   ├── services/
│   │   └── webhook_service.dart
│   ├── widgets/
│   │   ├── loading_spinner.dart
│   │   ├── success_animation.dart
│   │   └── error_animation.dart
│   └── constants/
│       └── app_constants.dart
├── pubspec.yaml
└── README.md
```

## ✅ Success Criteria

Het project is succesvol wanneer:

1. ✅ GitHub repository is aangemaakt met alle planning documenten
2. ✅ Alle 14 issues zijn aangemaakt en toegewezen
3. ✅ Flutter project is geïnitialiseerd en draait
4. ✅ App verschijnt in Android share menu
5. ✅ Share van TikTok werkt correct
6. ✅ Webhook ontvangt URLs in correct formaat
7. ✅ Success flow werkt (spinner → vinkje → auto-close)
8. ✅ Error flow werkt (spinner → kruis → retry)
9. ✅ App icon is geïmplementeerd
10. ✅ Release APK is gebouwd en getest
11. ✅ Documentatie is compleet
12. ✅ GitHub release is gepubliceerd

## 🎨 Design Specificaties

### Kleuren (Material Design)
- **Primary:** Blue (Material Blue)
- **Success:** Green 600 (#43A047)
- **Error:** Red 600 (#E53935)
- **Loading:** Blue 400 (#42A5F5)

### Animaties
- **Loading:** Circular spinner (continuous)
- **Success:** Scale animation met elastic curve (600ms)
- **Error:** Shake animation (400ms, 4Hz)

### Timing
- **Request Timeout:** 10 seconden
- **Success Display:** 1.5 seconden
- **Auto-Close Delay:** 2 seconden

## 🧪 Testing Strategie

### Functional Tests
- Share intent handling
- Webhook communication
- State transitions
- Auto-close behavior
- Retry functionality

### Device Tests
- Android 6.0 (minimum)
- Android 10+
- Android 13+

### Edge Cases
- No internet connection
- Webhook server offline
- Invalid URLs
- Multiple rapid shares

## 📊 Project Timeline

| Week | Focus | Deliverables |
|------|-------|--------------|
| Week 1 | Setup & Foundation | Repository, Issues, Basic structure |
| Week 2 | Core Development | UI components, Webhook service, Main screen |
| Week 3 | Polish & Testing | Icon, Testing, Bug fixes |
| Week 4 | Release | Documentation, Release APK, GitHub release |

## 🤝 Rollen & Verantwoordelijkheden

### PM Mode
- Repository aanmaken
- Issues aanmaken en beheren
- Milestone tracking
- Release management

### Code/Advanced Mode
- Feature implementatie
- Bug fixes
- Code reviews
- Testing

### Ask Mode
- Technische vragen beantwoorden
- Documentatie verduidelijken
- Best practices adviseren

## 📞 Support & Resources

### Documentatie
- PROJECT_PLAN.md - Architectuur en overzicht
- TECHNICAL_SPEC.md - Code voorbeelden
- IMPLEMENTATION_GUIDE.md - Workflow en checklist
- GITHUB_ISSUES.md - Gedetailleerde tasks

### External Resources
- [Flutter Documentation](https://flutter.dev/docs)
- [Android Share Intent Guide](https://developer.android.com/training/sharing/receive)
- [n8n Webhook Documentation](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.webhook/)

## 🎯 Key Decisions

| Decision | Rationale |
|----------|-----------|
| Flutter | Cross-platform potential, fast development |
| Android-only v1.0 | Focus on MVP, iOS later |
| No authentication | Simplicity, webhook is internal |
| Auto-close after success | Seamless UX, return to TikTok |
| Material Design | Native Android look & feel |
| minSdkVersion 23 | Balance between coverage and features |

## ⚠️ Risks & Mitigations

| Risk | Impact | Mitigation |
|------|--------|------------|
| Webhook downtime | High | Retry mechanism, clear error messages |
| Share intent not working | High | Thorough testing, fallback handling |
| Poor animation performance | Medium | Test on low-end devices, optimize |
| Large APK size | Low | Use release build, optimize assets |

## 🔮 Future Enhancements (Post v1.0.0)

1. **iOS Support** - Expand to iPhone users
2. **Multi-Platform** - Instagram, YouTube support
3. **Offline Queue** - Save URLs when offline
4. **Custom Webhook** - User-configurable endpoint
5. **Share History** - View past shared recipes
6. **Categories** - Tag recipes by type
7. **Favorites** - Mark favorite recipes
8. **Search** - Search through saved recipes

## 📝 Notes

- Alle planning documenten zijn klaar voor gebruik
- GitHub repository moet eerst aangemaakt worden via PM mode
- Issues kunnen direct aangemaakt worden uit GITHUB_ISSUES.md
- Code voorbeelden in TECHNICAL_SPEC.md zijn production-ready
- Volg IMPLEMENTATION_GUIDE.md voor dagelijkse workflow

---

**Status:** ✅ Planning Compleet - Klaar voor implementatie

**Volgende Actie:** Schakel naar PM mode om GitHub repository aan te maken