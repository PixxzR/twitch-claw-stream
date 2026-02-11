<p align="center">
  <img src="https://img.shields.io/badge/OpenClaw-Skills-8B5CF6?style=for-the-badge&logo=data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAyNCAyNCIgZmlsbD0id2hpdGUiPjxwYXRoIGQ9Ik0xMiAyQzYuNDggMiAyIDYuNDggMiAxMnM0LjQ4IDEwIDEwIDEwIDEwLTQuNDggMTAtMTBTMTcuNTIgMiAxMiAyem0wIDE4Yy00LjQyIDAtOC0zLjU4LTgtOHMzLjU4LTggOC04IDggMy41OCA4IDgtMy41OCA4LTggOHoiLz48L3N2Zz4=&logoColor=white" alt="OpenClaw Skills" />
  <img src="https://img.shields.io/badge/Twitch-Integration-9146FF?style=for-the-badge&logo=twitch&logoColor=white" alt="Twitch" />
  <img src="https://img.shields.io/badge/Skills-11-00D4AA?style=for-the-badge" alt="11 Skills" />
  <img src="https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge" alt="MIT License" />
</p>

<h1 align="center">
  <br>
  <code>twitch-claw-stream</code>
  <br>
</h1>

<h3 align="center">
  Turn your Twitch stream into an AI-powered interactive experience.<br>
  11 OpenClaw skills. Zero traditional code. Pure prompt engineering.
</h3>

<p align="center">
  <a href="#-skills-overview">Skills</a> &bull;
  <a href="#-quick-start">Quick Start</a> &bull;
  <a href="#-commands">Commands</a> &bull;
  <a href="#%EF%B8%8F-architecture">Architecture</a> &bull;
  <a href="#-adapt-it">Adapt It</a>
</p>

---

## The idea

Your AI agent sits in your Twitch chat. It watches, reacts, and runs interactive experiences — quizzes, bets, live website building, AI debates, mood-based music — all autonomously. Viewers interact through chat commands. The agent handles everything.

**No server. No database. No backend. Just an OpenClaw agent with 11 skill files.**

---

## ✨ Skills overview

### Interactive games

| Skill | Description |
|:------|:------------|
| **claw-quiz** | Adaptive quiz engine — generates questions based on stream topic, tracks ClawPoints, persistent leaderboard, speed bonus, seasonal resets |
| **claw-bet** | Virtual betting with ClawCoins — pari-mutuel payouts, wallet persistence, full economy simulation |
| **claw-vote** | Smart polling — manual polls + auto-detects debates in chat and creates votes on the fly |
| **claw-duel** | AI vs AI battles — two personas (ALPHA vs BETA) debate any topic, chat votes the winner |

### Content & ambiance

| Skill | Description |
|:------|:------------|
| **cognitive-stream-journal** | The stream's brain — observes chat, publishes mood bulletins every ~50 messages, generates a full markdown journal at end of stream |
| **claw-recap** | *"Previously on..."* — reads the last journal and delivers a cinematic recap for newcomers |
| **live-site-claw** | Viewers contribute ideas, code, text, or ASCII art via chat — the agent generates HTML, git commits, and deploys a community website in real-time |
| **mood-dj** | Analyzes chat sentiment every ~30 messages and switches Spotify playlists to match the vibe — with anti-flip-flop protection |
| **anime-react-claw** | AI commentary during anime watch parties — theories, cultural references, Easter egg spotting, spoiler detection |

### Stream management

| Skill | Description |
|:------|:------------|
| **raid-coordinator** | End-of-stream raid assistant — scores community streams by affinity, lets chat vote on the target |
| **stream-coordinator** | Meta-skill — rate limit enforcement, game mutual exclusion, memory cleanup, emergency kill switch |

---

## 🚀 Quick start

### Prerequisites

- [OpenClaw](https://openclaw.ai) installed (`npm install -g openclaw@latest`)
- Twitch OAuth token ([get one here](https://twitchtokengenerator.com))
- Spotify Premium + [spotify_player](https://github.com/aome510/spotify-player) (optional, for mood-dj)

### Install

```bash
git clone https://github.com/PixxzR/twitch-claw-stream.git
cd twitch-claw-stream

# Install Twitch plugin
openclaw plugins install @openclaw/twitch

# Copy skills to OpenClaw
cp -r skills/* ~/.openclaw/skills/

# Copy config template and fill in your tokens
cp config/openclaw-twitch.json ~/.openclaw/openclaw.json
```

Edit `~/.openclaw/openclaw.json` — replace all `TON_*` placeholders with your values.

> Full step-by-step instructions in [`config/setup-guide.md`](config/setup-guide.md)

### Launch

```bash
openclaw start
```

The agent joins your Twitch chat and all skills activate automatically.

---

## 🎮 Commands

### Games *(mutually exclusive — one at a time)*

```
!quiz [theme]          Start a quiz (mod)
!r <answer>            Answer (all)
!score                 Your stats (all)
!leaderboard           Top 5 (all)

!bet "description"     Open a bet (mod)
!bet-oui <amount>      Bet YES (all)
!bet-non <amount>      Bet NO (all)
!bet resolve oui/non   Resolve (mod)
!coins                 Your balance (all)

!vote Q | A | B        Start poll (mod)
!v1 !v2 !v3 !v4       Vote (all)
!qv "question"         Quick yes/no vote (mod)
!oui / !non            Quick vote answer (all)

!duel "subject"        Start AI debate (mod)
!voteA / !voteB        Vote for a side (all)
```

### Content & ambiance

```
!contrib <content>     Add to community site (all)
!site                  Show site URL (all)
!vibe <mood>           Vote for music mood (all)
!dj <mood>             Force music change (mod)
!dj off / !dj on       Toggle auto-DJ (mod)
!anime "title"         Start watch party mode (mod)
!theory <text>         Propose anime theory (all)
!recap                 Recap last stream (mod)
```

### Management

```
!claw status           Show all skills state (mod)
!claw off              Kill switch — silence everything (streamer)
!claw on               Reactivate (streamer)
!raid                  Get raid suggestions (mod)
!raid add @user cat    Add to community list (mod)
```

---

## ⚙️ Architecture

```
Twitch Chat
    │
    ▼
OpenClaw Gateway + @openclaw/twitch
    │
    ├─ requireMention: false → agent sees ALL messages
    │
    ├─ stream-coordinator (meta)
    │   ├─ Rate limit budget: 15 msgs / 30s (safe margin on Twitch's 20)
    │   ├─ Mutual exclusion: only 1 game active
    │   └─ Memory cap: 150 message buffer
    │
    ├─ Message-count triggers (no timers)
    │   ├─ cognitive-stream-journal → bulletin every 50 msgs
    │   ├─ mood-dj → analyze mood every 30 msgs
    │   ├─ claw-quiz → answer window = 15 msgs
    │   ├─ claw-bet → betting window = 30 msgs
    │   └─ claw-vote → voting window = 30 msgs
    │
    └─ Native tools used
        ├─ twitch.send     → post to chat
        ├─ read / write    → persistent JSON state
        ├─ exec            → git push, spotify_player
        ├─ memory          → session buffer
        └─ web_search      → raid suggestions
```

### Key design decisions

**Message-count triggers, not timers.** OpenClaw is message-driven — no background event loop. *"Every 50 messages"* guarantees activity-based pacing instead of silent timer misfires.

**Whitelist HTML sanitization.** The live-site-claw accepts viewer HTML but uses a strict tag whitelist (`div`, `span`, `p`, `pre`...) instead of a blacklist. Blocks SVG events, CSS `url()`, entity encoding bypasses.

**Prefixed bet commands.** `!bet-oui` / `!bet-non` instead of `!oui` / `!non` to avoid collision with quick votes.

**Atomic file updates.** All JSON state (leaderboard, wallets) uses immediate read → compute → write with no interleaved operations.

---

## 📁 Project structure

```
twitch-claw-stream/
│
├── skills/                          11 OpenClaw SKILL.md files
│   ├── cognitive-stream-journal/    Chat observer + journal writer
│   ├── claw-recap/                  "Previously on..." generator
│   ├── claw-quiz/                   Quiz engine + leaderboard
│   ├── claw-bet/                    Virtual betting system
│   ├── claw-vote/                   Polls + debate auto-detect
│   ├── claw-duel/                   AI vs AI debates
│   ├── live-site-claw/              Community site builder + deployer
│   ├── mood-dj/                     Sentiment-based music control
│   ├── anime-react-claw/            Watch party commentary
│   ├── raid-coordinator/            Raid suggestions + community list
│   └── stream-coordinator/          Rate limits + coordination
│
├── community-site/
│   └── index.html                   Tailwind template (auto-deployed)
│
├── stream-data/
│   ├── journals/                    Cognitive journals (markdown)
│   ├── duels/                       Duel history (json)
│   ├── quizzes/leaderboard.json     Persistent scores
│   ├── bets/wallets.json            ClawCoins balances
│   ├── bets/history.jsonl           Bet log
│   └── mood-logs/                   Music change log
│
└── config/
    ├── openclaw-twitch.json         Config template
    └── setup-guide.md               Full setup walkthrough
```

---

## 🔧 Adapt it

This project is designed to be forked and customized.

**Change the personality** — every skill prompt is in plain markdown. Edit the tone, language, references.

**Adjust pacing** — message-count thresholds (50, 30, 15) can be tuned for your chat velocity. Small stream? Lower them. Big stream? Raise them.

**Swap music** — edit the Spotify playlist URIs in `mood-dj/SKILL.md`.

**Add your community** — populate `raid-coordinator/community.json` with your streamer friends.

**Create new skills** — drop a folder with a `SKILL.md` in `skills/` and it's live.

---

## 🛡️ Safety

- All tokens are in `openclaw.json` (gitignored in prod), never in skill files
- `allowFrom` whitelist restricts who can trigger mod commands
- HTML contributions are sanitized via strict tag/attribute whitelist
- Virtual currencies (ClawCoins, ClawPoints) have zero monetary value
- `!claw off` instantly silences all skills
- No personal data is stored beyond public Twitch usernames

---

## License

MIT — do whatever you want with it.

---

<p align="center">
  <sub>11 skills &bull; 1,336 lines of prompt engineering &bull; 0 lines of traditional code</sub>
  <br>
  <sub>Built with <a href="https://openclaw.ai">OpenClaw</a> + <a href="https://claude.ai">Claude</a></sub>
</p>
