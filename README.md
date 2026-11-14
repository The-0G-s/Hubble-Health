# Hubble Health

A cross-platform (iOS, Android, web) Expo app built from the `Hubbel.txt` component:
a daily health check-in and guidance companion for long-duration space missions.

## Screens

- **Today**: daily progress across six readings (blood pressure, heart rate, glucose, temperature, sleep, mood).
- **Check-in → Health review**: enter readings, then see each one compared with your personal reference ranges.
- **Health**: dashboard with the latest blood pressure, a 7-reading trend, and every indicator's latest value.
- **Measures**: step-by-step measurement guides, spaceflight health-risk guidance and the **Space health library** (summaries of public NASA Human Research Program and Canadian Space Agency resources, each linked to the readings you track and to the original pages; content lives in `src/library.js`).
- **Mission**: body-system notes, dated skin/symptom photos, Bluetooth device discovery, nutrition log, private reflections, daily routine.
- **Profile**: name, mission start date (drives "flight day") and personal reference ranges.

All data stays on the device (AsyncStorage; localStorage on web), and Profile has a "Delete all my data" button. The web demo adds four sample check-ins on first launch; phone installs start empty.

## Publishing to the App Store and Google Play

See **[store/STORE_RELEASE.md](store/STORE_RELEASE.md)** for the step-by-step guide. The `store/` folder also has the privacy policy draft, listing text, screenshots, the Play icon and the feature graphic.

## Run it

Requires Node 20+.

```bash
npm install
npm run web        # opens in the browser
npm start          # then scan the QR code with Expo Go on your phone, or press i / a for a simulator
```

Build a static website (output in `dist/`, host it on any static host):

```bash
npx expo export --platform web
```

Build installable iOS/Android apps with EAS: `npx eas-cli@latest build`.

## How the original code was adapted

The original file was written for an app builder that injected a `platform-hooks` module.
`src/platform-hooks.js` re-implements that API locally:

| Hook | Implementation |
| --- | --- |
| `useStorage(key, default)` | Persistent key/value state shared across screens (AsyncStorage) |
| `useQuery(table, filter, order)` / `useMutation(table, 'insert')` | Small on-device table store with sorting and live updates |
| `seedSampleRows` | Adds the sample rows once on first launch; resolves `@day(-4 14:11)` dates |
| `useCamera` | `expo-image-picker` (camera and photo library); on phones each photo is copied into the app's documents folder so the OS cannot clear it |
| `useBluetooth` | Web Bluetooth device chooser in Chrome/Edge. The "Wearable discovery" section is hidden in the phone apps (native BLE would need a library such as `react-native-ble-plx`) |

`src/HealthMonitor.js` is the original code with these changes:

- The wrapper function was removed so screens are defined once at module level (previously they were re-created on every render of the root).
- Imports now come from Expo packages (`@expo/vector-icons` instead of `@react-native-vector-icons/ionicons`).
- Readability fixes: icons, the active tab, status chips and the selected mood used `#102A56` on a near-black background and were almost invisible; they now use a light blue (`highlightColor`). Text typed into the Profile fields and check-in notes was also dark-on-dark, and the status bar used dark text.
- The web date picker no longer overflows its card.

This app organizes observations; it does not diagnose or replace medical advice.
