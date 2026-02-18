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

If you want to play a xCloud title instead of Console streaming, you can use the NRO forwarder for the moonlight switch port.

Game Title - Will be the name of the NRO forwarder
Host IP - I just treat this as a dummy value to maintain compatibility with the moonlight nro forwarder. So just set 127.0.0.1 or some other iP addr.
AppID - this is where we select the xCloud title. So for example Kingdom Come: Deliverance, enter KINGDOMCOMEDELIVERANCE - Not sure what the best way to get this from. I used dev tools to find the request when starting the title from xbox.com/play


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
