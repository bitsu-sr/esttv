# Est TV - Android TV Digital Signage App

Een Android TV app voor digital signage die menu-foto's toont met offline caching, remote configuratie en monitoring.

## Features

### ✅ Geïmplementeerd

1. **Offline werking & Caching**
   - Lokale caching van foto's met Coil
   - Room database voor metadata
   - WorkManager voor periodieke sync (dagelijks)
   - Fallback naar laatste beschikbare foto's bij geen internet
   - Connectivity monitoring

2. **Remote Configuratie**
   - Firebase Remote Config voor slide duration en transition type
   - Firestore database voor playlists en photos
   - Actieve playlist ID configuratie

3. **Content Rotation**
   - Automatische slideshow met configurable duration
   - Crossfade transitions tussen foto's
   - Respecteert foto volgorde uit Firestore
   - Pre-loading van volgende foto's

4. **Android TV Specifiek**
   - `CATEGORY_LEANBACK_LAUNCHER` intent filter
   - Wake lock om screen aan te houden
   - Boot receiver voor auto-start
   - Immersive fullscreen mode
   - Landscape orientation

5. **Architectuur**
   - MVVM pattern met Repository laag
   - Kotlin Coroutines voor async operaties
   - Hilt voor dependency injection
   - Clean architecture principes

## Setup

### 1. Firebase Configuratie

1. Maak een Firebase project aan in de [Firebase Console](https://console.firebase.google.com/)
2. Voeg een Android app toe aan je Firebase project
3. Download `google-services.json` en plaats het in `app/` directory
4. Zorg dat je Firebase project de volgende services heeft ingeschakeld:
   - Cloud Firestore (vereist)
   - Remote Config (vereist)
   - Firebase Storage (optioneel - vereist Blaze plan)
   - Cloud Messaging (optioneel voor toekomstige features)

**Belangrijk**: Firebase Storage is **niet vereist**. De app werkt perfect met HTTP/HTTPS URLs. Zie `FIREBASE_SETUP.md` voor details.

### 2. Firestore Database Structuur

Maak de volgende collecties aan in Firestore:

#### `/playlists/{playlistId}`
```json
{
  "name": "Menu Photos",
  "active": true,
  "lastUpdated": 1234567890
}
```

#### `/photos/{photoId}`
```json
{
  "url": "https://example.com/images/menu/photo1.jpg",
  "order": 0,
  "active": true,
  "scheduledFrom": null,
  "scheduledTo": null,
  "playlistId": "your-playlist-id"
}
```

**Let op**: Gebruik HTTPS URLs (niet `gs://` URLs) om Firebase Storage te vermijden. De app download en cache deze automatisch.

### 3. Firebase Remote Config

Configureer de volgende Remote Config parameters:

- `slide_duration_seconds`: Aantal seconden tussen foto's (default: 5)
- `transition_type`: Type transition (default: "fade")
- `active_playlist_id`: ID van de actieve playlist

### 4. Build & Run

```bash
./gradlew build
./gradlew installDebug
```

## Project Structuur

```
app/src/main/java/com/example/esttv/
├── data/
│   ├── cache/          # Image caching logic
│   ├── local/          # Room database (DAOs, Database)
│   ├── model/          # Data models (Photo, Playlist)
│   ├── remote/         # Firebase data sources
│   └── repository/     # Repository layer
├── di/                 # Hilt dependency injection modules
├── ui/
│   ├── screen/        # Compose screens
│   ├── theme/         # App theme
│   └── viewmodel/     # ViewModels
├── worker/             # WorkManager workers
├── BootReceiver.kt     # Auto-start on boot
├── EstTVApplication.kt # Application class
└── MainActivity.kt     # Main activity
```

## Libraries

- **Firebase**: Firestore, Remote Config, Messaging, Crashlytics (Storage optioneel)
- **Room**: Local database voor offline caching
- **WorkManager**: Periodieke content sync
- **Hilt**: Dependency injection
- **Coil**: Image loading en caching
- **Coroutines**: Async operaties
- **Compose**: UI framework voor Android TV

## Toekomstige Features

- Firebase Cloud Messaging voor remote commands
- Heartbeat systeem voor monitoring
- Screenshot functionaliteit
- Geavanceerde scheduling features
- Web dashboard voor content management

## Notities

- De app gebruikt `fallbackToDestructiveMigration()` voor development. Verwijder dit in production en implementeer proper migrations.
- **Firebase Storage is optioneel**: Gebruik HTTP/HTTPS URLs voor foto's om Firebase Storage (en Blaze plan) te vermijden.
- Test de app op een fysieke Android TV device of emulator met TV support.
- Zie `FIREBASE_SETUP.md` voor gedetailleerde setup instructies.

## Licentie

Copyright © 2024 Est TV

