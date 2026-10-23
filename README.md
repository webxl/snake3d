# SNAKE·3D

A first-person 3D snake game. Fly through a glass arena, eat glowing orbs and
grow — but don't touch the walls, your own body or a rival's body. Includes four
corner spectator cameras, a cinematic death replay, mine power-ups, and 1v1
multiplayer with optional bots.

## Play

Open `index.html` in a modern browser. Three.js and the `Reflector` helper are
loaded from CDNs, so there is no build step.

You can serve it locally with any static server:

```sh
python3 -m http.server 5173
# then open http://localhost:5173/
```

### Controls

| Input | Action |
| --- | --- |
| Mouse | Steer (yaw + pitch) |
| Shift / right click | Boost |
| Space / left click | Launch a mine (after collecting a pink power-up) |
| F | Toggle wall reflections |
| Esc | Pause (solo) / release the pointer (multiplayer) |

## Solo

You and three CPU rivals (VIPER, COBRA, MAMBA). Crash and your body bursts into
snake-coloured food that the rivals eat; the arena keeps simulating while you
watch. From the game-over panel you can watch a replay of the final seconds.

## Multiplayer

Host-authoritative 1v1 (+ 0–3 bots) timed respawn deathmatch:

- 3-minute match, 3-second respawn, highest score wins (length breaks ties).
- The arena and food count scale with the number of snakes.
- The host simulates everything and streams snapshots at 20 Hz; the guest sends
  inputs at 30 Hz and renders interpolated snapshots.
- A tiny Cloudflare Worker + Durable Object pairs players and relays WebRTC
  signalling. If a direct DataChannel can't be established, the same room
  relays game traffic (the lobby shows "Connected (relayed)").

### Coordinator

```sh
cd mp
npx wrangler dev --port 8787
```

Open the game in two tabs (or two machines on the LAN), choose **Multiplayer**,
host a challenge, and join with the 5-character code or the `?join=CODE` link.

Deploy it with:

```sh
cd mp
npx wrangler deploy
```

Then point the game at the deployed worker by setting `MP_COORD_PROD` in
`index.html`, or pass it at runtime: `?coordinator=wss://…`. The page must be
served over HTTPS for `wss://` to work.

See [`mp/README.md`](mp/README.md) for the coordinator protocol.

## Files

- `index.html` — the entire game (renderer, simulation, UI, netcode)
- `mp/` — Cloudflare Worker + Durable Object challenge coordinator
