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

> **Note:** this repository contains only the skill instructions (`SKILL.md`). If your project already has a video made with this skill, it copies that folder and adapts it; otherwise it creates the files above in `videos/<name>/`.

### Requirements

- Chrome (default path `C:/Program Files/Google/Chrome/...`, or set `CHROME=`)
- `ffmpeg` on the `PATH`
- Node.js 24 — no `npm install` needed

### What you'll need for each video

- **Where it will be posted**: defines the format (9:16, 4:5, 1:1 or 16:9)
- **The message**: the text or script, and the call to action at the end
- **Brand**: colors, fonts and logo (as an image file); if they're already in the project, the skill reads them from there
- **Images** (optional): screenshots, photos or product shots to show in the video
- **Voice-over** (optional): a recording of the narration (any audio file) and an OpenAI API key to time each word with Whisper, passed only as an environment variable, never saved
- **No voice-over**: the desired duration (vignette 3–6s, intro 5–10s, explainer 30–90s)
- **Real data only**: numbers, results and testimonials must come from you; the skill never makes them up

### Installation

Install with the [skills.sh](https://skills.sh) CLI (works with Claude Code and other agents):

```sh
# in the current project
npx skills add Felipeex/motion-skill

# or for all your projects (user-level)
npx skills add Felipeex/motion-skill -g
```

<details>
<summary>Manual install (git clone)</summary>

```sh
# personal (all projects)
git clone https://github.com/Felipeex/motion-skill ~/.claude/skills/motion

# or per project
git clone https://github.com/Felipeex/motion-skill .claude/skills/motion
```

</details>

### Usage

Ask Claude Code for things like *"make a motion video"*, *"animated intro"*, *"vignette"*, *"animated explainer"*, or *"kinetic text"*. The skill will:

1. Ask up to 6 short questions (format, voice-over, duration, style, brand, sound/CTA)
2. Propose a script table (time | line or text | what appears) and wait for your approval
3. Send review stills, then deliver the final MP4

```sh
cd videos/<name>
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

> **Observação:** este repositório contém só as instruções da skill (`SKILL.md`). Se o seu projeto já tiver um vídeo feito com esta skill, ela copia essa pasta e adapta; senão, cria os arquivos acima em `videos/<nome>/`.

### Requisitos

- Chrome (caminho padrão `C:/Program Files/Google/Chrome/...`, ou defina `CHROME=`)
- `ffmpeg` no `PATH`
- Node.js 24 — sem `npm install`

### O que você precisa para cada vídeo

- **Onde vai ser postado**: define o formato (9:16, 4:5, 1:1 ou 16:9)
- **A mensagem**: o texto ou roteiro e a chamada final
- **Marca**: cores, fontes e logo (em arquivo de imagem); se já estiverem no projeto, a skill lê de lá
- **Imagens** (opcional): prints, fotos ou imagens do produto para aparecer no vídeo
- **Narração** (opcional): a gravação da fala (qualquer arquivo de áudio) e uma chave da API da OpenAI para marcar o tempo de cada palavra com o Whisper, passada só por variável de ambiente, nunca salva
- **Sem narração**: a duração desejada (vinheta 3–6s, intro 5–10s, explicativo 30–90s)
- **Só dados reais**: números, resultados e depoimentos precisam vir de você; a skill nunca inventa

### Instalação

Instale com a CLI do [skills.sh](https://skills.sh) (funciona no Claude Code e em outros agentes):

```sh
# no projeto atual
npx skills add Felipeex/motion-skill

# ou para todos os seus projetos (nível de usuário)
npx skills add Felipeex/motion-skill -g
```

<details>
<summary>Instalação manual (git clone)</summary>

```sh
# pessoal (todos os projetos)
git clone https://github.com/Felipeex/motion-skill ~/.claude/skills/motion

# ou por projeto
git clone https://github.com/Felipeex/motion-skill .claude/skills/motion
```

</details>

### Uso

Peça ao Claude Code coisas como *"faz um motion"*, *"vídeo animado"*, *"vinheta"*, *"intro"*, *"explicativo animado"* ou *"texto animado"*. A skill vai:

1. Fazer no máximo 6 perguntas curtas (formato, narração, duração, estilo, marca, som/chamada final)
2. Propor um roteiro em tabela (tempo | fala ou texto | o que aparece) e esperar o seu ok
3. Mandar fotos de revisão e depois entregar o MP4

```sh
cd videos/<nome>
node export.mjs --quadros 0,2,5,9   # fotos em review/
node export.mjs                     # MP4
```
