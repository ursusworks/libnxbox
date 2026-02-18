# LibNXbox

**Xbox Cloud Streaming client for Nintendo Switch** — Stream from your console or xCloud to your Nintendo Switch

![Platform](https://img.shields.io/badge/platform-Nintendo%20Switch-e60012)
![Language](https://img.shields.io/badge/language-C99-blue)
![License](https://img.shields.io/badge/status-alpha-orange)

## Download
Grab the libnxbox.nro under releases

## Important!
Needs to run in title mode, will crash with applet mode.

## Launching Home streaming (Stream from your own console)
Just launch the .nro in title mode. It will discover your console, in the same way that xbox.com/play does.
I have not tested what happends if you have more than one Xbox connected to your user. Not sure what will happend :) (The world won't burn, but we might struggle to choose the right xbox and stall the connect)

## Launching xCloud titles
If you want to play a xCloud title instead of Console streaming, you can use the NRO forwarder for the moonlight switch port: https://nsp-forwarder.vercel.app/moonlight

- Game Title - Will be the name of the NRO forwarder
- Host IP - I just treat this as a dummy value to maintain compatibility with the moonlight nro forwarder. So just set 127.0.0.1 or some other IP addr.
- AppID - this is where we select the xCloud title. So for example Assassins Creed: Shadows, enter ASSASSINSCREEDSHADOWS - Not sure what the best way to get this from. I used dev tools to find the request when starting the title from xbox.com/play

Example of an NRO forwarder:

![Screenshot](./NSP_EX.png)

Install the .NSP file and once you launch the game on the switch, that xCloud title should be launched. (xCloud title takes a little longer to launch in my experience, about 50 sec.)

Some titleIds that I've seen:
- ASSASSINSCREEDSHADOWS
- KINGDOMCOMEDELIVERANCE
- ARCRAIDERS

So might be that it's all caps and all special characters stripped. So Assassin's Creed: Shadows becomes ASSASSINSCREEDSHADOWS

But the only way of knowing is to launch a title from xbox.com/play with dev-tools open, with HAR checked. Look at the network tab and look for the titleId there

![Screenshot](./DEVTOOLS.png)

## Troubleshooting

* xCloud title takes a little longer to load than console streaming. A little under a minute, is what I've seen while testing
* Console streaming was the main goal from the start, full xCloud was an easy target after WebRTC was achieved. So it's less tested than console streaming, and might be more unstable
* If you want to connect to your console outside of LAN and struggle to conneect, make sure that you can access your console on UDP port 9002 (port forwarding)
* libnxbox.nro will create a debug.log file. So if anything goes haywire, the log might have a clue.

## Authentication

All authentication goes through Microsofts official servers. My application stores nothing but the token. And that token is stored on your sd card. I will NEVER see or know about your user information.

When you first launch the nro it will check to see if there is a token.dat file stored on the sd card (/switch/libnxbox/token.dat). If there is no such file. It will promt you to sign-in
![Screenshot](./AUTHWINDOW.png)

When the token.dat file is created, it will use this to refresh MSAL tokens going forward. So unless you delete the file or the stored token becomes invalid, you wont see this screen again.

## Button Mapping

Face buttons are mapped by **physical position** (not label), so the right-side button on Switch maps to the right-side button on Xbox, etc.

| Switch | Xbox | Position |
|--------|------|----------|
| A | B | Right |
| B | A | Bottom |
| X | Y | Top |
| Y | X | Left |
| L | LB | Left bumper |
| R | RB | Right bumper |
| ZL | LT (full) | Left trigger (digital → 0 or 100%) |
| ZR | RT (full) | Right trigger (digital → 0 or 100%) |
| L-Stick click | L3 | Left stick press |
| R-Stick click | R3 | Right stick press |
| D-Pad | D-Pad | Directional |
| − | View/Select | |
| + (short press) | Menu/Start | < 500 ms hold |
| + (long press) | Nexus/Guide | ≥ 500 ms hold |

### Analog Sticks

- Range: −32768 to 32767 (matches Xbox format)
- Deadzone: ±2000 (values within deadzone snap to 0)
- Y-axis convention: up = positive (same on both platforms)

### Triggers

The Switch has digital ZL/ZR buttons (no analog travel). They are mapped as fully pressed (65535) or released (0).

### Rumble 

Work in progress

## Architecture

```
Authentication          GameStream API          WebRTC Connection
─────────────          ──────────────          ─────────────────
MSAL Device Code  ──►  Create Session    ──►   SDP Offer/Answer
MSA Token         ──►  Exchange ICE      ──►   ICE Candidates
Xbox User Token        Keepalive               DTLS-SRTP Handshake
XSTS Token                                     SCTP Data Channels
GSSV Token                                     Video/Audio/Input
```
Video

| Parameter | Value |
|-----------|-------|
| Resolution | 1280×720 (720p) |
| Quality preset | Very High |
| Codec | H.264 |
| Hardware decode | Tegra X1 NVDEC |
| Rendering | deko3d NV12→RGB shader, triple-buffered |
| Colorspace | BT.709 |

Audio

| Parameter | Value |
|-----------|-------|
| Codec | Opus (WebM container) |
| Sample rate | 48 kHz |
| Channels | Stereo |
| Latency target | ~60 ms |


## Disclaimer

This application is **unofficial homebrew software** and is **not affiliated with, endorsed by, or supported by Nintendo Co., Ltd., Microsoft Corporation, Xbox, or any of their subsidiaries or partners**.

Nintendo, Nintendo Switch, Microsoft, Xbox, and all related names, logos, and trademarks are the property of their respective owners and are used here for identification purposes only.

This software is provided **“as is”**, without warranty of any kind, express or implied. The authors are not responsible for any damage, data loss, system instability, or other issues that may result from the use or misuse of this application.

By using this software, you acknowledge that:
- You are responsible for complying with all applicable laws and regulations in your region.
- You understand the risks associated with running homebrew software on your device.
- You assume full responsibility for any consequences arising from its use.

If you do not agree with these terms, **do not use this software**.
