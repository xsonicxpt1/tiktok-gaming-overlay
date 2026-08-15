# TikTok Gaming Live Overlay Pack

Overlay pack 9:16 para TikTok Live Studio / OBS (portrait) — tema gaming com webcam, stats, eventos recentes e alertas animados.

## Estrutura

```
tiktok-gaming-overlay/
├── main.html           # Overlay principal (game capture + webcam + UI)
├── alerts.html         # Overlay de alertas (follow/gift/sub/like/share)
├── config.json         # Configuração única (tema, layout, URLs, sons)
├── README.md           # Este arquivo
├── .gitignore
└── assets/
    └── sfx/            # Sons de alerta (opcional, pode usar CDN)
```

## Deploy Rápido (GitHub Pages — 2 min)

```bash
cd tiktok-gaming-overlay
git init
git add .
git commit -m "Initial gaming overlay pack"
git branch -M main
git remote add origin https://github.com/xsonicxpt1/tiktok-gaming-overlay.git
git push -u origin main
# GitHub: Settings → Pages → Deploy from branch → main / (root) → Save
# Aguarde 2-5 min
# URLs finais:
# Main:  https://xsonicxpt1.github.io/tiktok-gaming-overlay/main.html
# Alerts: https://xsonicxpt1.github.io/tiktok-gaming-overlay/alerts.html
```

## Configuração (config.json)

Edite `config.json` e faça push — atualiza ao vivo em ~1 min (cache bust automático).

### Campos principais

| Campo | Descrição |
|-------|-----------|
| `githubUsername` | Seu user GitHub (para URLs CDN dos sons) |
| `repoName` | Nome do repo (`tiktok-gaming-overlay`) |
| `tiktokHandle` | Seu @handle (ex: `@seuusuario`) |
| `currentGame` | Nome do jogo atual |
| `theme.primary` | Cor principal (rosa TikTok: `#FF0050`) |
| `theme.accent` | Cor secundária (cyan: `#00F2EA`) |
| `theme.blurRadius` | Blur RGPD (ex: `8px`) |
| `urls.gameFrame` | URL do game capture (iframe) |
| `layout.webcam.enabled` | `true`/`false` |
| `alerts.sfx.*` | URLs dos sons (use jsDelivr CDN ou hospede local) |

## TikTok Live Studio — Adicionar Browser Sources

| Source | URL | Dimensões | FPS | CSS Customizado |
|--------|-----|-----------|-----|-----------------|
| **Main** | `https://SEU_USUARIO.github.io/tiktok-gaming-overlay/main.html` | 1080×1920 | 30 | (vazio) |
| **Alerts** | `https://SEU_USUARIO.github.io/tiktok-gaming-overlay/alerts.html` | 1080×1920 | 30 | `body { pointer-events: none; }` |

> ⚠️ **HTTPS obrigatório** — TikTok bloqueia HTTP/localhost. GitHub Pages dá HTTPS grátis.

## Testar Alertas Localmente

Abra no navegador:
```
https://SEU_USUARIO.github.io/tiktok-gaming-overlay/alerts.html?event=follow&user=TesteUser
https://SEU_USUARIO.github.io/tiktok-gaming-overlay/alerts.html?event=gift&user=Maria&gift=Rosa&count=3
https://SEU_USUARIO.github.io/tiktok-gaming-overlay/alerts.html?event=subscribe&user=Pedro
https://SEU_USUARIO.github.io/tiktok-gaming-overlay/alerts.html?event=like&user=Ana
https://SEU_USUARIO.github.io/tiktok-gaming-overlay/alerts.html?event=share&user=Joao
```

## Integração com Bots/Conectores (postMessage)

Do seu bot/conector TikTok, envie para o iframe de alertas:

```js
// Referência ao iframe de alertas no TikTok Live Studio
const alertsFrame = document.querySelector('iframe[src*="alerts.html"]').contentWindow;

alertsFrame.postMessage({
  type: 'TIKTOK_ALERT',
  event: 'follow',        // follow | gift | subscribe | like | share
  user: 'username',
  gift: 'Nome do Presente',  // opcional (para gift)
  count: 1                // opcional (para gift múltiplo)
}, '*');
```

Para atualizar stats do main overlay:
```js
const mainFrame = document.querySelector('iframe[src*="main.html"]').contentWindow;
mainFrame.postMessage({ type: 'TIKTOK_STATS', followers: 1234, viewers: 56, likes: 789 }, '*');
mainFrame.postMessage({ type: 'TIKTOK_GAME', game: 'Elden Ring' }, '*');
mainFrame.postMessage({ type: 'TIKTOK_EVENT', event: 'follow', user: 'user123' }, '*');
```

## Controles

| Ação | Tecla / Botão |
|------|---------------|
| Alternar blur (RGPD) | Tecla **B** ou clique no botão `BLUR`/`CLARO` |
| Webcam | Auto-inicia se `layout.webcam.enabled: true` |

## Game Capture (iframe)

O `main.html` usa iframe para o game capture. **Muitos sites bloqueiam iframe** (`X-Frame-Options: SAMEORIGIN`).

### Opções que FUNCIONAM:
1. **scrcpy** (recomendado): Espelha celular via USB → capture janela scrcpy no OBS
2. **Game capture nativo OBS**: Use OBS game capture → Browser Source aponta para overlay local
3. **Página própria**: Hospede sua página de jogo que permite embed

### scrcpy setup:
```bash
# Install: https://github.com/Genymobile/scrcpy/releases
scrcpy --video-codec=h264 --max-size=1080
# No OBS/TikTok Live Studio: Add Source → Window Capture → scrcpy window
```

## RGPD / Privacidade

- Blur no game frame (iframe) via `filter: blur(var(--blur-radius))`
- Aviso visível: "Conteúdo desfocado por privacidade (RGPD)"
- Botão toggle acessível sem foco (clique + tecla B + postMessage)
- Atende requisitos de não mostrar strangers identificáveis

## Sons de Alerta (SFX)

Coloque `.mp3` em `assets/sfx/` e faça push, ou use CDN no `config.json`:

```json
"sfx": {
  "follow": "https://cdn.jsdelivr.net/gh/USER/REPO@main/assets/sfx/follow.mp3",
  "gift": "https://cdn.jsdelivr.net/gh/USER/REPO@main/assets/sfx/gift.mp3",
  ...
}
```

Sons gratuitos: https://freesound.org / https://mixkit.co/free-sound-effects/game/

## Checklist Pré-Live

- [ ] `config.json` editado com seu handle (@xsonicxpty), jogo, URLs
- [ ] Deploy no GitHub Pages feito e HTTPS carregando (https://xsonicxpt1.github.io/tiktok-gaming-overlay/main.html)
- [ ] Ambas URLs testadas no navegador (sem erros console)
- [ ] Webcam pede permissão → concede → vídeo aparece
- [ ] Alertas testados via query string (`?event=follow&user=teste`)
- [ ] Botão BLUR funciona (tecla B + clique)
- [ ] Browser Sources adicionadas no TikTok Live Studio
- [ ] Hard refresh (Ctrl+Shift+R) após mudanças no config
- [ ] Clique uma vez no overlay de alertas (desbloqueia áudio)

## Problemas Comuns

| Problema | Solução |
|----------|---------|
| Iframe branco / não carrega | Site bloqueia embed → use scrcpy ou game capture nativo |
| Sons não tocam | Clique no overlay de alertas uma vez antes de ir live (autoplay policy) |
| TikTok Live Studio não carrega overlay | Verifique HTTPS (não HTTP), hard refresh, modo incógnito |
| Webcam não aparece | Permissão negada → ícone cadeado na barra URL → permitir câmera |
| Config não atualiza | Cache do GitHub Pages (1-2 min) + hard refresh no Studio |
| Layout quebrado em 720p | Teste responsivo: redimensione janela do navegador |

## Personalização Avançada

- **Cores**: Edite `theme.primary`, `theme.accent` no config.json
- **Posições**: Ajuste `layout.webcam`, `layout.liveBadge`, etc. (px base 1080×1920)
- **Fontes**: Troque `@font-face` no CSS (Orbitron → Rajdhani, Audiowide, etc.)
- **Animações**: Modifique `@keyframes` no CSS (reduced-motion respeitado)

## Licença

MIT — use livremente, inclusive comercial.