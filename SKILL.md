---
name: motion
description: Cria qualquer vídeo em motion design (canvas + Web Audio → MP4), em qualquer formato (9:16, 4:5, 1:1, 16:9), com ou sem narração, para qualquer marca ou assunto. Use quando pedirem "motion", "vídeo animado", "animação", "vinheta", "intro", "explicativo animado" ou "texto animado".
---

# Motion genérico (canvas + Web Audio → MP4)

Um vídeo, sem amarrar formato, marca, nicho ou narração. Se o projeto já tiver um vídeo feito
com esta skill, **copie a pasta dele** para `videos/<nome>/` (ou onde o usuário quiser), apague o
que é do vídeo antigo (`assets/`, `voice.wav`, `timings.json`, `words.json`, o MP4) e reescreva as
cenas do `video.html`: o exportador, o servidor local e o `buildAudio` já funcionam. Se não houver,
crie os arquivos abaixo nessa pasta.

| Arquivo | O que faz |
|---|---|
| `trim-audio.mjs` | (Só com narração) tira os silêncios → `voice.wav` normalizada + `timings.json` |
| `video.html` | A animação: `render(t)` num canvas + trilha/efeitos em Web Audio. Aberto no Chrome, vira prévia com som |
| `export.mjs` | Chrome sem janela desenha cada quadro → ffmpeg → MP4. `--quadros 0.2,9.5,…` só tira fotos para revisar |
| `assets/` | Imagens que o vídeo usa (logo, prints, fotos) |

Precisa de: Chrome em `C:/Program Files/Google/Chrome/...` (ou `CHROME=`), ffmpeg no PATH, Node 24. Sem npm install.

## O que adaptar ao copiar

- **Formato**: `W`, `H` e `<canvas width height>` no `video.html`, o `aspect-ratio` do CSS do canvas e o `--window-size` do `export.mjs` (maior que o canvas). Medidas comuns: 1080x1920 (Reels/Stories/Shorts), 1080x1350 (feed 4:5), 1080x1080 (1:1), 1920x1080 (YouTube/apresentação).
- **Nome da saída**: `OUT` no `export.mjs` (`video-<nome>.mp4`) e o prefixo do `--user-data-dir`.
- **Duração**: `TOTAL`. Com narração, é a duração da voz cortada; sem narração, combine com o usuário (vinheta 3–6s, intro 5–10s, explicativo 30–90s).
- **Sem narração**: tire o carregamento de `voice.wav` e o ducking da música no `buildAudio`; a trilha fica no volume cheio (≈0,6–0,75). Sem som nenhum: pule o `renderAudioWav` e exporte só o vídeo (`-an`).
- **Visual**: cores e fontes vêm da marca do usuário (pergunte ou leia do projeto). Defina tudo como constantes no topo (`COLORS`, `FONTS`); nada de cor solta no meio das cenas.
- **Zonas seguras**: 9:16 → conteúdo entre y≈200 e y≈1600 e 90px nas laterais (interface do Reels). 4:5 e 1:1 → 60px de margem. 16:9 → 5% de margem (title safe).

## Fluxo com o usuário (fale simples)

1. **Perguntas, no máximo 6, uma por vez** (AskUserQuestion, 3 opções, a recomendada primeiro). Pergunte só o que não dá para descobrir: formato/onde vai ser postado, tem narração?, duração (se não houver voz), estilo, marca/cores (se não estiverem no projeto), som e chamada final.
2. **Roteiro em tabela** (tempo | fala ou texto | o que aparece). **Espere o ok** antes de desenhar.
3. Mande fotos de revisão, depois entregue o MP4.

## Áudio (quando houver narração)

- `node trim-audio.mjs "<gravação>"`: silencedetect a -35dB/0,25s, folgas de 0,08s/0,14s e `loudnorm`. Confira a média da voz perto de -19 dB (`volumedetect`).
- **Tempo de cada palavra**: Whisper (`whisper-1`, `verbose_json`, `timestamp_granularities[]=word`, idioma certo) com a chave que o usuário der, **só por variável de ambiente**, nunca salva. Palavras rápidas às vezes vêm no mesmo tempo: espalhe à mão.
- Os textos na tela seguem **o que foi falado**. Avise quando diferirem do roteiro escrito.

## Animação (`video.html`)

- **Tudo sai de `render(t)`**: a mesma `t` gera sempre o mesmo quadro. Aleatório só com `mulberry(seed)`; nada de `Math.random()` ou `Date` no desenho.
- **Tempos em constantes no topo**; o som (`soundEvents`) lê as mesmas constantes, então imagem e som não saem do ritmo.
- **Cenas**: `SCENES = [[início, fim, fn]]`, entrada ~0,3s e saída ~0,22s, com uma transição cobrindo a troca. Evite corte seco.
- **Texto palavra por palavra**: `drawWords`. Espaço entre palavras = `0.28 * size` (com 0,24, itálico gruda). Ajuste `size` ao formato: em 16:9 o texto é mais largo e menor.
- **Movimento constante**: nenhuma cena parada por mais de 0,5s (luzes do fundo, leve zoom de câmera, granulado).
- **Quadro 0 = capa**: algo já visível em t=0 (use entradas com tempo negativo); nada de abrir com tela branca.
- Fontes: `document.fonts.load(...)` de cada peso antes do primeiro `render`; `window.ready` espera fontes e imagens.
- **Imagens por http, nunca `file://`** (o canvas fica "sujo" e `toDataURL` falha). O `export.mjs` já sobe um servidor local.
- Logo do cliente sempre como imagem, nunca redesenhada.

## Som (Web Audio)

- `buildAudio(ac, voz, t0)` serve para a prévia (`AudioContext`) e para exportar (`OfflineAudioContext` → WAV).
- Trilha sintetizada (pad, baixo, bumbo, chimbal); ajuste o BPM ao clima (≈80 calmo, ≈96 padrão, ≈120 energético). Com voz: música a 0,22 sob a voz. Efeitos: whoosh nas transições, pop nas entradas, tic em contagens. Tudo passa por um compressor.
- Saída com `loudnorm=I=-14:TP=-1.5` (redes sociais). Para YouTube/apresentação, -14 também serve.

## Exportar e revisar

```sh
cd videos/<nome>
node export.mjs --quadros 0,2,5,9   # fotos em review/
node export.mjs                     # MP4
```

- **Olhe as fotos antes de exportar.** Para um mosaico: copie como `seq_00.jpg…` e use `ffmpeg -i seq_%02d.jpg -vf "scale=400:-1,tile=7x2"` (glob não funciona no Windows).
- Checklist: texto cobrindo texto, palavra grudada, conteúdo fora da zona segura, quadro 0 vazio, cena parada, número sem ponto de milhar.
- Depois do MP4: `ffprobe` (resolução certa, 30fps, h264+aac), `ebur128` (≈ -14 LUFS) e quadros tirados do próprio MP4 (`-ss`). Diga ao usuário que você mediu o som, mas não ouviu.
- Não versione `mix.wav` nem `review/` (confira o `.gitignore` se a pasta for nova).

## Conteúdo

- Prova, números e depoimentos só se forem reais e o usuário fornecer. Nunca invente.
- Se o vídeo for anúncio, promessa de ganho ("até 4x", "R$ X por dia") pode ser reprovada pelo Meta. Avise.
