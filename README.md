# CG-STEVE — Transformações Geométricas 2D

Projeto da disciplina de Computação Gráfica (UNICAP) — Transformações 2D com HTML +
JavaScript + Canvas 2D.

## Integrantes

- Guilherme Maciel
- Guilherme Raposo

## Tema

O **Steve acena** para quem está olhando. Ele é desenhado com `fillRect` — cabeça,
tronco, braços e pernas — e pode ser transladado, girado, escalado e espelhado
interativamente.

## Como abrir

Abra o `index.html` direto no navegador (duplo clique). Não precisa de servidor.

## Como o Steve é montado

```text
             40
          ┌──────┐
          │cabeça│ 40        y = -160  topo
    ┌──┬──┼──────┼──┬──┐
    │br│  │tronco│  │br│ 60  y = -120  ombro
    └──┴──┼──────┼──┴──┘
          │pn│pn │      60   y =  -60  quadril
          └──┴───┘           y =    0  chão
```

A origem `(0,0)` fica entre os pés. No Canvas o eixo `y` cresce para baixo, então o
corpo todo ocupa `y` negativo.

## Arquivos

| arquivo | o que faz |
| --- | --- |
| `js/config.js` | medidas, cores, pivôs e estado inicial |
| `js/steve.js` | desenha o Steve e aplica as transformações |
| `js/controles.js` | sliders, teclado e painel da matriz |
| `js/main.js` | `desenha()`: zera a matriz e desenha um quadro |
| `js/animacao.js` | roteiro da animação automática |
| `js/botoes.js` | botões de animação e de mostrar/esconder controles |

## Onde cada transformação é usada

| requisito | onde no código |
| --- | --- |
| 1. Translação (`ctx.translate`) | `steve.js` — posiciona o Steve, desenha a **mesma** `desenhaPerna()` nos dois lados e leva cada pivô à origem |
| 2. Rotação (`ctx.rotate`) | `steve.js` — corpo, braço e mão |
| 3. Escala (`ctx.scale`) | `steve.js` — redimensiona o Steve inteiro |
| 4. Composição | `steve.js` — `M = T · R · S` na mesma matriz |
| 5. Ponto fixo `T → Op → T⁻¹` | `steve.js` — 3 casos: centro do corpo, ombro e cotovelo |
| 6. Animação | `animacao.js` — `requestAnimationFrame`; o reset `setTransform(1,0,0,1,0,0)` está no `desenha()` do `main.js`, chamado a cada quadro |
| 7. Pilha de estados | `steve.js` — `save`/`restore` em cada perna, no braço, na mão; e `setTransform` no `main.js` |

### O padrão do ponto fixo

O Canvas aplica as transformações **de baixo para cima** — a última linha escrita é a
primeira que acontece:

```js
ctx.translate(OMBRO.x, OMBRO.y);     // 3a op: volta
ctx.rotate(anguloBraco);             // 2a op: gira
ctx.translate(-OMBRO.x, -OMBRO.y);   // 1a op: leva o ombro à origem
desenhaBracoSuperior();
```

Equivale a `M = T(p) · R(θ) · T(−p)`.

## Bônus

- **Interatividade** — sliders de posição, rotação, escala, ombro e cotovelo; teclado:
  setas movem, `Q`/`E` giram, `+`/`−` escalam, `A`/`D` movem o ombro, `F` espelha,
  `R` reseta. Em `controles.js` e `botoes.js`.
- **Reflexão** — `ctx.scale(-1, 1)` em `steve.js`, o caso especial da escala com fator
  negativo. O tchau troca de lado.
- **Hierarquia de transformações** — a mão é desenhada **dentro** do `save`/`restore`
  do braço, então herda a rotação do ombro e ainda gira sozinha no cotovelo. É o caso
  "a mão acompanha o braço".
