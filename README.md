# LibNXbox

**Xbox GameStream client for Nintendo Switch** — stream games from your Xbox console to your Switch over LAN or WAN using WebRTC.

![Platform](https://img.shields.io/badge/platform-Nintendo%20Switch-e60012)
![Language](https://img.shields.io/badge/language-C99-blue)
![License](https://img.shields.io/badge/status-alpha-orange)

## Overview

Needs to run in title mode, will crash with applet mode.
If you just run the NRO, it will ask you to sign in with Xbox using a device code at https://microsoft.com/link

Once that is done, it will use the same logic as xbox.com/play to discover you Xbox, connect and start the stream.

I have not tested what happends if you have more than one Xbox connected to your user. Not sure what will happend :) (The world won't burn, but we might struggle to choose the right xbox and stall the connect)

If you want to play a xCloud title instead of Console streaming, you can use the NRO forwarder for the moonlight switch port: https://nsp-forwarder.vercel.app/moonlight

- Game Title - Will be the name of the NRO forwarder
- Host IP - I just treat this as a dummy value to maintain compatibility with the moonlight nro forwarder. So just set 127.0.0.1 or some other iP addr.
- AppID - this is where we select the xCloud title. So for example Kingdom Come: Deliverance, enter KINGDOMCOMEDELIVERANCE - Not sure what the best way to get this from. I used dev tools to find the request when starting the title from xbox.com/play

## Troubleshooting

* xCloud title takes a little longer to load than console streaming. A little under a minute, is what I've seen while testing
* If you want to connect to your console outside of LAN and struggle to conneect, make sure that you can access your console on UDP port 9002 (port forwarding)
* libnxbox.nro will create a debug.log file. So if anything goes haywire, the log might have a clue.

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

## Disclaimer

This application is **unofficial homebrew software** and is **not affiliated with, endorsed by, or supported by Nintendo Co., Ltd., Microsoft Corporation, Xbox, or any of their subsidiaries or partners**.

Nintendo, Nintendo Switch, Wii, Wii U, Microsoft, Xbox, and all related names, logos, and trademarks are the property of their respective owners and are used here for identification purposes only.

This software is provided **“as is”**, without warranty of any kind, express or implied. The authors are not responsible for any damage, data loss, system instability, or other issues that may result from the use or misuse of this application.

By using this software, you acknowledge that:
- You are responsible for complying with all applicable laws and regulations in your region.
- You understand the risks associated with running homebrew software on your device.
- You assume full responsibility for any consequences arising from its use.

If you do not agree with these terms, **do not use this software**.
