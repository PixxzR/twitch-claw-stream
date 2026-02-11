# Setup Guide — Twitch Claw Stream

## Pre-requis

- macOS (M1+) ou Linux
- Node.js 22+
- Git configuré avec SSH key (pour push auto)
- Compte Twitch (streamer + bot si tu veux un compte séparé)
- Compte Spotify Premium (pour MoodDJ)

---

## 1. Installer OpenClaw

```bash
npm install -g openclaw@latest
openclaw onboard --install-daemon
```

Doc officielle : https://docs.openclaw.ai/getting-started

**Vérifier l'installation** :
```bash
openclaw --version   # Doit afficher une version
openclaw status      # Doit montrer la gateway
```

## 2. Installer le plugin Twitch

```bash
openclaw plugins install @openclaw/twitch
```

## 3. Obtenir un token OAuth Twitch

1. Va sur https://dev.twitch.tv/console/apps
2. Crée une application :
   - Nom : "YpixxzrClawBot" (ou le nom de ton choix)
   - URL de redirection : `http://localhost:3000`
   - Catégorie : Chat Bot
3. Note le **Client ID**
4. Génère un token OAuth avec les scopes : `chat:read chat:edit`
   - Utilise https://twitchtokengenerator.com ou le flow OAuth Twitch
5. Note le **Access Token** et le **Refresh Token**

### Trouver ton Twitch User ID

Méthode rapide :
```bash
curl -s "https://api.twitch.tv/helix/users?login=TON_PSEUDO" \
  -H "Authorization: Bearer TON_ACCESS_TOKEN" \
  -H "Client-Id: TON_CLIENT_ID" | python3 -m json.tool
```
Le champ `"id"` est ton User ID. Fais pareil pour tes mods.

## 4. Configurer

1. Copie le fichier de config :
```bash
cp ~/Projects/twitch-claw-stream/config/openclaw-twitch.json ~/.openclaw/openclaw.json
```

2. Ouvre `~/.openclaw/openclaw.json` et remplace :
   - `TON_BOT_USERNAME` → le pseudo Twitch du bot
   - `TON_TOKEN_ICI` → ton access token (commence par `oauth:`)
   - `TON_REFRESH_TOKEN_ICI` → ton refresh token
   - `TON_CLIENT_ID_ICI` → ton Client ID
   - `#ypixxzr` → `#ton_channel`
   - `TON_TWITCH_USER_ID` → ton User ID numérique
   - `MOD1_ID`, `MOD2_ID` → les User IDs de tes mods

3. **Vérification** : aucune valeur ne doit encore contenir "TON_" :
```bash
grep -c "TON_" ~/.openclaw/openclaw.json
# Doit afficher 0
```

## 5. Installer les skills

```bash
# Copie tous les skills dans le dossier partagé OpenClaw
cp -r ~/Projects/twitch-claw-stream/skills/* ~/.openclaw/skills/
```

**Vérifier** :
```bash
ls ~/.openclaw/skills/
# Doit lister : anime-react-claw  claw-bet  claw-duel  claw-quiz
# claw-recap  claw-vote  cognitive-stream-journal  live-site-claw
# mood-dj  raid-coordinator  stream-coordinator
```

## 6. Préparer le community site (pour LiveSiteClaw)

### 6a. Créer le repo GitHub
1. Va sur https://github.com/new
2. Nom : `twitch-community-site` (public)
3. Ne coche PAS "Initialize with README"

### 6b. Connecter le repo local
```bash
cd ~/Projects/twitch-claw-stream/community-site
git remote add origin git@github.com:TON_USERNAME/twitch-community-site.git
git branch -M main
git push -u origin main
```

### 6c. Connecter Cloudflare Pages
1. Va sur https://dash.cloudflare.com → Pages
2. "Create a project" → "Connect to Git"
3. Sélectionne le repo `twitch-community-site`
4. Build command : (laisser vide, c'est du HTML statique)
5. Output directory : `/`
6. Deploy

### 6d. Vérifier
```bash
cd ~/Projects/twitch-claw-stream/community-site
git remote get-url origin  # Doit afficher l'URL du repo
git push --dry-run          # Doit réussir sans erreur
```

**Si le push échoue** : vérifie ta SSH key (`ssh -T git@github.com`).

## 7. Préparer MoodDJ

### 7a. Installer le contrôleur Spotify
```bash
brew install spotify_player
```

### 7b. Authentifier Spotify
```bash
spotify_player
# Suis les instructions d'authentification dans le terminal
# Ferme après avoir confirmé que ça marche
```

### 7c. Créer les playlists
Crée 7 playlists dans ton Spotify (ou utilise des existantes) :
- CHILL, FOCUS, HYPE, EPIC, NOSTALGIA, LOFI, CHAOS

### 7d. Récupérer les URIs
Pour chaque playlist : clic droit → "Share" → "Copy Spotify URI"
Format : `spotify:playlist:37i9dQZF1DX...`

### 7e. Mettre les URIs dans le skill
Ouvre `~/.openclaw/skills/mood-dj/SKILL.md` et remplace les PLACEHOLDER :
```
CHILL     → spotify:playlist:TON_URI_CHILL
FOCUS     → spotify:playlist:TON_URI_FOCUS
...
```

### 7f. Vérifier
```bash
spotify_player playback start uri spotify:playlist:TON_URI_CHILL --shuffle
# La musique doit se lancer
```

## 8. Vérification finale avant launch

Checklist :
```bash
# OpenClaw installé et running
openclaw status

# Plugin Twitch
openclaw plugins list | grep twitch

# Config sans placeholders
grep -c "TON_\|PLACEHOLDER" ~/.openclaw/openclaw.json ~/.openclaw/skills/mood-dj/SKILL.md

# Git remote configuré pour community site
cd ~/Projects/twitch-claw-stream/community-site && git remote get-url origin

# Spotify fonctionne
which spotify_player && echo "OK" || echo "MANQUANT"

# Dossiers de data existent
ls ~/Projects/twitch-claw-stream/stream-data/{journals,duels,quizzes,bets,recaps,mood-logs}

# Skills installés
ls ~/.openclaw/skills/ | wc -l  # Doit être 11
```

## 9. Lancer

```bash
openclaw start
```

L'agent rejoint automatiquement le chat Twitch et active tous les skills.

## 10. Tester (dans ton chat Twitch)

Teste chaque skill dans l'ordre :

| Ordre | Commande | Skill testé | Attendu |
|-------|----------|-------------|---------|
| 1 | `!claw status` | stream-coordinator | Affiche l'état de tous les skills |
| 2 | `!recap` | claw-recap | "Pas de journal précédent" (premier stream) |
| 3 | `!quiz ia` | claw-quiz | Pose une question sur l'IA |
| 4 | `!r <réponse>` | claw-quiz | Vérifie ta réponse |
| 5 | `!bet "Test pari"` | claw-bet | Ouvre un pari |
| 6 | `!bet-oui 10` | claw-bet | Mise acceptée |
| 7 | `!bet resolve oui` | claw-bet | Résolution du pari |
| 8 | `!coins` | claw-bet | Affiche ton solde |
| 9 | `!qv "Test quick vote"` | claw-vote | Quick vote ouvert |
| 10 | `!duel "IA vs Humains"` | claw-duel | Duel lancé |
| 11 | `!contrib msg: Premier test` | live-site-claw | Contribution intégrée |
| 12 | `!site` | live-site-claw | URL du site |
| 13 | `!vibe hype` | mood-dj | Vote pour le mood |
| 14 | `!dj hype` | mood-dj | Force le mood |
| 15 | `!anime "Cowboy Bebop"` | anime-react-claw | Mode watch party |
| 16 | `!raid list` | raid-coordinator | Liste vide |
| 17 | `!raid add @ami dev` | raid-coordinator | Ajout confirmé |
| 18 | `!claw off` | stream-coordinator | Mode silencieux |
| 19 | `!claw on` | stream-coordinator | Skills réactivés |

## Structure des données

```
stream-data/
├── journals/          # Journaux cognitifs par stream (markdown)
├── duels/             # Historique des duels (json)
├── quizzes/
│   └── leaderboard.json   # Scores persistants
├── bets/
│   ├── wallets.json       # Soldes ClawCoins
│   └── history.jsonl      # Historique des paris
├── recaps/            # Recaps générés
└── mood-logs/         # Logs des changements musicaux (jsonl)
```

## Troubleshooting

| Problème | Solution |
|----------|---------|
| Bot ne rejoint pas le chat | Vérifie le token OAuth, les scopes, et le nom de channel dans la config |
| `!contrib` ne deploy pas | Vérifie `git remote get-url origin` dans community-site/ |
| Musique ne change pas | Vérifie `which spotify_player` et l'auth Spotify |
| Rate limit (messages droppés) | Réduis l'activité simultanée, utilise `!claw status` pour voir le budget |
| Agent ne répond à rien | Vérifie `openclaw status`, et que `requireMention` est bien `false` |
| Scores disparus | Vérifie que `wallets.json` et `leaderboard.json` ne sont pas corrompus |
