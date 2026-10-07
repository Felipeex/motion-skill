# motion — Claude Code skill

[English](#english) · [Português (BR)](#português-br)

---

## English

A [Claude Code](https://claude.com/claude-code) skill for creating **motion design videos** of any kind — vignettes, intros, animated explainers, kinetic text — rendered with **canvas + Web Audio** and exported to **MP4**.

- Any format: 9:16 (Reels/Stories/Shorts), 4:5, 1:1, 16:9
- With or without voice-over (silence trimming, loudness normalization, word-level timing via Whisper)
- Any brand or subject: colors and fonts come from the user's brand
- Synthesized soundtrack and sound effects (whooshes, pops, ticks) through Web Audio
- Deterministic rendering: `render(t)` always produces the same frame for the same `t`
- Review frames before export, then validate the MP4 (`ffprobe`, `ebur128` ≈ -14 LUFS)

### How it works

| File | Role |
|---|---|
| `trim-audio.mjs` | (Voice-over only) trims silences → normalized `voice.wav` + `timings.json` |
| `video.html` | The animation: `render(t)` on a canvas + music/effects in Web Audio. Opens in Chrome as a live preview |
| `export.mjs` | Headless Chrome draws each frame → ffmpeg → MP4. `--quadros 0.2,9.5,…` only captures review stills |
| `assets/` | Images used by the video (logo, screenshots, photos) |

> **Note:** this repository contains only the skill instructions (`SKILL.md`). The skill expects a working engine (the files above) at `criativos/higienizador/video/` inside your project, which it copies and adapts for each new video.

### Requirements

- Chrome (default path `C:/Program Files/Google/Chrome/...`, or set `CHROME=`)
- `ffmpeg` on the `PATH`
- Node.js 24 — no `npm install` needed

### Installation

Copy the skill into your Claude Code skills folder:

```sh
# personal (all projects)
git clone https://github.com/Felipeex/motion-skill ~/.claude/skills/motion

# or per project
git clone https://github.com/Felipeex/motion-skill .claude/skills/motion
```

### Usage

Ask Claude Code for things like *"make a motion video"*, *"animated intro"*, *"vignette"*, *"animated explainer"*, or *"kinetic text"*. The skill will:

1. Ask up to 6 short questions (format, voice-over, duration, style, brand, sound/CTA)
2. Propose a script table (time | line or text | what appears) and wait for your approval
3. Send review stills, then deliver the final MP4

```sh
cd criativos/<name>/video
node export.mjs --quadros 0,2,5,9   # stills in review/
node export.mjs                     # MP4
```

> The skill instructions (`SKILL.md`) are written in Brazilian Portuguese.

---

## Português (BR)

Uma skill do [Claude Code](https://claude.com/claude-code) para criar **vídeos em motion design** de qualquer tipo — vinhetas, intros, explicativos animados, texto animado — renderizados com **canvas + Web Audio** e exportados em **MP4**.

- Qualquer formato: 9:16 (Reels/Stories/Shorts), 4:5, 1:1, 16:9
- Com ou sem narração (corte de silêncios, normalização de volume, tempo de cada palavra via Whisper)
- Qualquer marca ou assunto: cores e fontes vêm da marca do usuário
- Trilha e efeitos sonoros sintetizados (whoosh, pop, tic) com Web Audio
- Renderização determinística: `render(t)` gera sempre o mesmo quadro para o mesmo `t`
- Fotos de revisão antes de exportar e validação do MP4 (`ffprobe`, `ebur128` ≈ -14 LUFS)

### Como funciona

| Arquivo | O que faz |
|---|---|
| `trim-audio.mjs` | (Só com narração) tira os silêncios → `voice.wav` normalizada + `timings.json` |
| `video.html` | A animação: `render(t)` num canvas + trilha/efeitos em Web Audio. Aberto no Chrome, vira prévia com som |
| `export.mjs` | Chrome sem janela desenha cada quadro → ffmpeg → MP4. `--quadros 0.2,9.5,…` só tira fotos para revisar |
| `assets/` | Imagens que o vídeo usa (logo, prints, fotos) |

> **Observação:** este repositório contém só as instruções da skill (`SKILL.md`). A skill espera um motor pronto (os arquivos acima) em `criativos/higienizador/video/` dentro do seu projeto, que ela copia e adapta para cada vídeo novo.

### Requisitos

- Chrome (caminho padrão `C:/Program Files/Google/Chrome/...`, ou defina `CHROME=`)
- `ffmpeg` no `PATH`
- Node.js 24 — sem `npm install`

### Instalação

Copie a skill para a pasta de skills do Claude Code:

```sh
# pessoal (todos os projetos)
git clone https://github.com/Felipeex/motion-skill ~/.claude/skills/motion

# ou por projeto
git clone https://github.com/Felipeex/motion-skill .claude/skills/motion
```

### Uso

Peça ao Claude Code coisas como *"faz um motion"*, *"vídeo animado"*, *"vinheta"*, *"intro"*, *"explicativo animado"* ou *"texto animado"*. A skill vai:

1. Fazer no máximo 6 perguntas curtas (formato, narração, duração, estilo, marca, som/chamada final)
2. Propor um roteiro em tabela (tempo | fala ou texto | o que aparece) e esperar o seu ok
3. Mandar fotos de revisão e depois entregar o MP4

```sh
cd criativos/<nome>/video
node export.mjs --quadros 0,2,5,9   # fotos em review/
node export.mjs                     # MP4
```
