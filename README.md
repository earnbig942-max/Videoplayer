# Fire TV Video Remote

Contains:
- `firetv-player/`: native Kotlin Fire TV receiver/player APK source.
- `phone-remote/index.html`: HTML phone controller to package with FreeHTMLtoAPK.

## Important limitations
This is source code, not a prebuilt or device-tested APK. Fire OS/Android may block a foreground service from launching a full-screen activity while another app (such as YouTube) is in front. The service attempts the launch; this is not guaranteed. YouTube's previous playback position cannot be read or restored by another app.

Open Fire TV Video Player after each reboot. It starts a foreground receiver on port 8080. The phone sends commands locally; the Fire TV streams the supplied direct URL. No cloud storage is used.

## Build from your phone using GitHub Actions
1. Create a GitHub repository.
2. Upload the contents of this project to it (the `firetv-player` folder and `.github` folder must be at repository root).
3. Open Actions and run **Build Fire TV APK**.
4. Download the `firetv-debug-apk` artifact from the completed workflow.
5. Sideload `app-debug.apk` onto Fire TV.

The included workflow uses Gradle 8.9 and Java 17. A local build is `gradle :firetv-player:app:assembleDebug`.

## Install and use
1. Enable installation of unknown apps on Fire TV for your file manager/installer.
2. Install and open Fire TV Video Player.
3. Read the Fire TV IP and authentication token shown on screen.
4. Open `phone-remote/index.html` on your phone, or package it as an APK.
5. Enter the Fire TV IP, port 8080, and token. Press CONNECT.
6. Paste a direct MP4 or M3U8 URL and press PLAY. Use STOP to stop it.
7. Keep both devices on the same Wi-Fi. Router client/AP isolation must be disabled.

## FreeHTMLtoAPK requirements for phone controller
The wrapper must grant INTERNET permission and allow cleartext HTTP to local LAN addresses. If FreeHTMLtoAPK does not expose those settings, its APK may be unable to call `http://FIRE_TV_IP:8080`. Test the HTML in a browser that permits local HTTP, or use a wrapper that supports cleartext traffic.

## API
- `GET /status`
- `POST /play` with JSON `{"url":"https://host/path/video.mp4"}`
- `POST /stop` with JSON `{}`

Send `Authorization: Bearer TOKEN`.

## Troubleshooting
- Connection failure: check same Wi-Fi, IP, port, token, router isolation, and Fire TV receiver app.
- 401: recopy token from TV.
- Command accepted but no video: Fire OS may have blocked background activity launch. Bring Fire TV Video Player to foreground and retry; some Fire OS versions require an on-screen user action.
- Playback error: test a direct, publicly reachable MP4 using H.264 video and AAC audio. A webpage, protected/DRM stream, or link requiring cookies is not a direct playable URL.
