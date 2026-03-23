# 📱 Recepten Verzamelaar

Een eenvoudige Android app om TikTok recepten direct op te slaan in je receptenboek via een webhook.

## 🎯 Wat doet deze app?

Recepten Verzamelaar maakt het super makkelijk om recepten van TikTok op te slaan:

1. **Zie een leuk recept op TikTok** 🍳
2. **Druk op de Share knop** 📤
3. **Selecteer "Recepten Verzamelaar"** 📲
4. **Klaar!** ✅ Het recept wordt automatisch opgeslagen

## ✨ Features

- 🚀 **Supersnel** - Direct delen vanuit TikTok
- ✅ **Visuele feedback** - Zie direct of het gelukt is
- 🔄 **Retry functie** - Probeer opnieuw bij een fout
- 🎨 **Clean design** - Simpel en overzichtelijk
- 📱 **Lichtgewicht** - Kleine app, grote functionaliteit

## 📋 Vereisten

- Android 6.0 (API 23) of hoger
- Internetverbinding
- TikTok app (of andere apps die URLs delen)

## 🔧 Installatie

### Optie 1: Download APK (Aanbevolen)

1. Download de nieuwste APK van de [Releases](https://github.com/cinematenpodcast/recepten-verzamelaar/releases) pagina
2. Open het APK bestand op je Android apparaat
3. Sta installatie van onbekende bronnen toe als gevraagd
4. Volg de installatie instructies

### Optie 2: Build vanaf source

```bash
# Clone de repository
git clone https://github.com/cinematenpodcast/recepten-verzamelaar.git
cd recepten-verzamelaar

# Installeer dependencies
flutter pub get

# Build de APK
flutter build apk --release

# De APK staat nu in: build/app/outputs/flutter-apk/app-release.apk
```

## 📖 Gebruik

### Stap 1: Open TikTok
Open de TikTok app en zoek een recept dat je wilt opslaan.

### Stap 2: Deel het recept
Druk op de **Share** knop (pijltje naar rechts) in TikTok.

### Stap 3: Selecteer Recepten Verzamelaar
Kies **Recepten Verzamelaar** uit het share menu.

### Stap 4: Wacht op bevestiging
- ⏳ **Laad cirkel** - De URL wordt verstuurd
- ✅ **Groen vinkje** - Succesvol opgeslagen! (app sluit automatisch)
- ❌ **Rood kruis** - Er ging iets mis (druk op "Probeer Opnieuw")

## 🛠️ Technische Details

### Architectuur

```
TikTok App → Share Intent → Recepten Verzamelaar → Webhook (n8n)
```

### Webhook Endpoint

- **URL:** `https://n8n.cinematen.be/webhook/recepten`
- **Method:** POST
- **Content-Type:** application/json
- **Body:**
  ```json
  {
    "url": "https://www.tiktok.com/@user/video/1234567890"
  }
  ```

### Tech Stack

- **Framework:** Flutter 3.x
- **Language:** Dart
- **Minimum SDK:** Android 6.0 (API 23)
- **Dependencies:**
  - `http` - HTTP requests
  - `receive_sharing_intent` - Share intent handling
  - `flutter_animate` - Animations

## 🐛 Troubleshooting

### App verschijnt niet in share menu

1. Controleer of de app geïnstalleerd is
2. Herstart je telefoon
3. Probeer de TikTok app opnieuw te openen

### "Er ging iets mis" foutmelding

**Mogelijke oorzaken:**
- Geen internetverbinding
- Webhook server is offline
- Ongeldige URL

**Oplossingen:**
1. Controleer je internetverbinding
2. Druk op "Probeer Opnieuw"
3. Probeer het later opnieuw

### App sluit niet automatisch

Dit kan gebeuren als:
- De animatie nog bezig is (wacht 2 seconden)
- Er een fout is opgetreden

## 🔒 Privacy & Beveiliging

- ✅ Geen data wordt lokaal opgeslagen
- ✅ Alleen de URL wordt verstuurd naar de webhook
- ✅ HTTPS verbinding naar n8n.cinematen.be
- ✅ Geen tracking of analytics
- ✅ Geen persoonlijke gegevens verzameld

## 🚀 Development

### Setup Development Environment

```bash
# Installeer Flutter (zie https://flutter.dev/docs/get-started/install)

# Clone repository
git clone https://github.com/cinematenpodcast/recepten-verzamelaar.git
cd recepten-verzamelaar

# Installeer dependencies
flutter pub get

# Run in debug mode
flutter run

# Run tests
flutter test
```

### Project Structure

```
lib/
├── main.dart                      # App entry point
├── screens/
│   └── share_handler_screen.dart  # Main screen
├── services/
│   └── webhook_service.dart       # HTTP service
├── widgets/
│   ├── loading_spinner.dart       # Loading animation
│   ├── success_animation.dart     # Success checkmark
│   └── error_animation.dart       # Error cross
└── constants/
    └── app_constants.dart         # Configuration
```

### Build Commands

```bash
# Debug build
flutter build apk --debug

# Release build
flutter build apk --release

# Install on connected device
flutter install
```

## 🤝 Contributing

Contributions zijn welkom! Volg deze stappen:

1. Fork de repository
2. Maak een feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit je changes (`git commit -m 'Add some AmazingFeature'`)
4. Push naar de branch (`git push origin feature/AmazingFeature`)
5. Open een Pull Request

## 📝 Changelog

### Version 1.0.0 (2026-03-23)
- ✨ Initiële release
- ✅ Share intent handling
- ✅ Webhook integratie
- ✅ Success/error animaties
- ✅ Auto-close functionaliteit
- ✅ Retry mechanisme

## 📄 License

Dit project is gelicenseerd onder de MIT License - zie het [LICENSE](LICENSE) bestand voor details.

## 👤 Author

**Yorrick Schoonheydt**
- Website: [cinematen.be](https://cinematen.be)
- GitHub: [@cinematenpodcast](https://github.com/cinematenpodcast)

## 🙏 Acknowledgments

- Flutter team voor het geweldige framework
- n8n voor de webhook functionaliteit
- TikTok voor de inspiratie

## 📞 Support

Heb je vragen of problemen? 

- 📧 Open een [Issue](https://github.com/cinematenpodcast/recepten-verzamelaar/issues)
- 💬 Start een [Discussion](https://github.com/cinematenpodcast/recepten-verzamelaar/discussions)

---

**Gemaakt met ❤️ voor receptenliefhebbers**