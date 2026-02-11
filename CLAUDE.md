# CLAUDE.md — Contexte projet twitch-claw-stream

## Ce projet

11 skills OpenClaw pour transformer un stream Twitch en expérience interactive pilotée par une IA autonome. Tout est en SKILL.md (prompt engineering pur), zéro code traditionnel.

## Historique de conception (session initiale)

Ce projet a été conçu, implémenté, audité et corrigé en une seule session. Voici ce qui a été fait :

1. **Brainstorm** — 15+ idées innovantes OpenClaw x Twitch générées
2. **Sélection** — 10 skills retenus + 1 coordinateur méta
3. **Implémentation** — Tous les SKILL.md, config, community-site template, data structures
4. **Audit critique** — Analyse complète : 4 blocking, 7 high-risk, 12 medium-risk trouvés
5. **Corrections** — Tous les problèmes fixés :
   - Triggers temporels remplacés par comptage de messages (OpenClaw est message-driven)
   - Conflits de commandes résolus (!bet-oui au lieu de !oui)
   - Race conditions JSON avec atomic read-modify-write
   - Git error handling dans live-site-claw
   - HTML sanitization whitelist au lieu de blacklist
   - Rate limiting global via stream-coordinator
   - Exclusion mutuelle entre jeux interactifs
6. **Publication** — Repo GitHub public, README stylé, LICENSE MIT

## Skills (11)

- `cognitive-stream-journal` — Journal cognitif, bulletins tous les ~50 msgs
- `claw-recap` — "Previously on ypixxzrTV..."
- `claw-quiz` — Quiz adaptatif + ClawPoints + leaderboard
- `claw-bet` — Paris virtuels ClawCoins
- `claw-vote` — Sondages manuels + auto-detect débats
- `claw-duel` — Battles IA ALPHA vs BETA
- `live-site-claw` — Site communautaire construit live par le chat
- `mood-dj` — DJ IA mood-based via Spotify
- `anime-react-claw` — Commentateur anime watch party
- `raid-coordinator` — Suggestions de raid fin de stream
- `stream-coordinator` — Méta : rate limits, exclusion mutuelle, kill switch

## Points d'attention

- Tous les triggers sont basés sur le COMPTAGE DE MESSAGES, pas sur le temps
- Un seul jeu interactif à la fois (quiz/bet/vote/duel)
- Le budget rate limit Twitch est 15 msgs/30s (marge sur les 20 officiels)
- Les données persistantes sont dans stream-data/ (JSON)
- La community list du raid-coordinator est dans son propre dossier (community.json)
- Le channel Twitch est `#ypixxzr`

## Prochaines étapes

- Installer OpenClaw + plugin Twitch
- Remplir les tokens dans openclaw-twitch.json
- Setup le community-site sur GitHub + Cloudflare Pages
- Configurer Spotify + playlists par mood
- Tester (voir le tableau de test dans config/setup-guide.md)

## Repo

https://github.com/PixxzR/twitch-claw-stream
