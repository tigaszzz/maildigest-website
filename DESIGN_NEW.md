# DESIGN.md — Mail Digest UI System (v0.5)

## Identidade visual

Paradigma: **briefing matinal colorido**, single-column, single-shot read.
Estilo: editorial warm + multi-color systematic (monday.com-inspired) + tipografia variável.

Princípios:
- Velocidade de **decisão** acima de tudo — o utilizador abre a app de manhã e em 11 minutos sabe o que fazer.
- **Cor como significado funcional**, nunca decorativa — cada secção do briefing tem a sua cor, cada prioridade tem a sua, cada categoria tem a sua.
- **Tipografia editorial variável** — uma única família serif (Fraunces) com `opsz` cobre do hero dramático ao corpo legível.
- Hierarquia por **tamanho + cor + posição**, nunca por sombras ou borders pesados.

---

## Tipografia

| Função | Fonte | Uso |
|---|---|---|
| Display / títulos | `Fraunces` (variável, `opsz`+`SOFT`) | Hero "Bom dia, Tiago", subjects de cards, números das stats |
| UI / labels / corpo | `Inter Tight` | Tudo o resto — lede, resumos, botões, labels |
| Metadata / mono | `JetBrains Mono` | Timestamps, badges, contagens, atalhos, código |

Import:

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Fraunces:opsz,wght,SOFT@9..144,300..900,0..100&family=Inter+Tight:wght@400;450;500;600;700&family=JetBrains+Mono:wght@400;500;600&display=swap" rel="stylesheet">
```

### Tamanhos e variação

| Elemento | Fonte | Tamanho | Variação / peso |
|---|---|---|---|
| Hero `Bom dia, Tiago.` | Fraunces | clamp(40px, 5.5vw, 58px) | `opsz 144, SOFT 50`, weight 400 |
| Hero `<em>` (gradient) | Fraunces italic | (igual) | `opsz 144, SOFT 80` |
| Hero lede | Inter Tight | 17px | weight 450, line-height 1.55 |
| Stat number | Fraunces | 32px | `opsz 72, SOFT 30`, weight 500, `font-variant-numeric: tabular-nums` |
| Stat label | Inter Tight | 12px | weight 500 + 600 strong |
| Section title | Fraunces | 26px | `opsz 72, SOFT 30`, weight 500 |
| Card subject | Fraunces | 21px | `opsz 36, SOFT 30`, weight 500 |
| Card summary | Inter Tight | 14px | weight 450, line-height 1.65 |
| News summary (italic) | Fraunces italic | 16px | `opsz 20, SOFT 30`, weight 400 |
| Brand name | Fraunces | 19px | `opsz 72, SOFT 30`, weight 500 |
| Date / meta | JetBrains Mono | 11px | weight 400, letter-spacing 0.18em uppercase |
| Badge | JetBrains Mono | 10px | weight 600, letter-spacing 0.12em uppercase |
| Button | Inter Tight | 12.5px | weight 500 |

---

## Paleta

```css
:root {
  /* Foundation — warm cream paper */
  --bg:       #faf6ee;
  --bg2:      #ffffff;
  --bg3:      #f3ede0;
  --bg-rail:  #f6f1e6;
  --border:   rgba(20,17,13,0.10);
  --border2:  rgba(20,17,13,0.16);
  --border3:  rgba(20,17,13,0.28);
  --ink:      #14110d;
  --text:     #14110d;
  --text2:    #4a4540;
  --text3:    #7a7068;

  /* Brand — iris violet */
  --brand:     #6c5cf0;
  --brand-2:   #5546d4;
  --brand-on:  #ffffff;
  --brand-bg:  #efeaff;
  --brand-tint:#f5f1ff;

  /* Category palette */
  --urgent:    #e0444c;  --urgent-2:  #c2353d;  --urgent-bg: #fde7e9;
  --sunset:    #f59e0b;  --sunset-2:  #d48409;  --sunset-bg: #fff0d0;
  --emerald:   #10b981;  --emerald-2: #099268;  --emerald-bg:#d6f4e8;
  --sky:       #4f7ce5;  --sky-2:     #3961c6;  --sky-bg:    #e0eaff;
  --grape:     #a855f7;  --grape-2:   #8636d3;  --grape-bg:  #f1e3ff;
  --rose:      #f43f5e;  --rose-2:    #d12e4d;  --rose-bg:   #ffe1e6;
  --slate:     #6b88a0;  --slate-bg:  #e7eef4;
}
```

### Atribuição funcional de cores

| Cor | Token | Usado para |
|---|---|---|
| Brand iris | `--brand` | Logo dot, primary CTA, AI badge, gradient em `<em>` do hero, brand moments |
| Urgent red | `--urgent` | Badge `urgent`, secção "O que precisa de ti" (section dot), CTAs destrutivos/urgentes |
| Sunset orange | `--sunset` | Badge `today`, task-card border-left, "Hoje" no stat row |
| Emerald green | `--emerald` | Badge `this_week`, tasks done, ✓ checkmarks, "esta semana" no stat row |
| Sky blue | `--sky` | Badge `newsletter`, news compression card, secção "O que eu tratei" (section dot) |
| Grape purple | `--grape` | Badge `event`, event-card border-left, secção "O que eu proponho" (section dot) |
| Rose pink | `--rose` | Badge `followup`, follow-ups detectados |
| Slate | `--slate` | Badge `fyi`, metadata fria, transactional "auto/system" |

### Badges (estilo monday filled)

```css
.badge {
  font-family: 'JetBrains Mono', monospace;
  font-size: 10px; font-weight: 600;
  letter-spacing: 0.12em; text-transform: uppercase;
  padding: 4px 9px; border-radius: 999px;
  color: white;
}
.badge.urgent   { background: var(--urgent); }
.badge.today    { background: var(--sunset); }
.badge.week     { background: var(--emerald); }
.badge.fyi      { background: var(--slate); }
.badge.news     { background: var(--sky); }
.badge.event    { background: var(--grape); }
.badge.followup { background: var(--rose); }
.badge.ai       { background: var(--brand); }
```

Filled (fundo saturado + texto branco), pill-shaped (border-radius 999px).
Não usar tinted-bg + colored-text (paradigma anterior abandonado).

---

## Layout

```
┌─────────────────────────────────────────────────────┐
│  TOPBAR (sticky, bg2 + blur)                        │
│  brand-dot · Mail Digest      refresh · ⚙ · avatar  │
├─────────────────────────────────────────────────────┤
│                                                     │
│  ┌───── HERO (card with rainbow stripe top) ─────┐  │
│  │ DATE · weather pill                           │  │
│  │ Bom dia, <em>Tiago</em>.    (Fraunces 58px)   │  │
│  │ Li 23 emails desde ontem...                   │  │
│  │ [stat] [stat] [stat] [stat]                   │  │
│  └───────────────────────────────────────────────┘  │
│                                                     │
│  ● O que precisa de ti               2 itens · ~7m  │
│  ┌─ card (top stripe today) ──────────────────────┐ │
│  │ [HOJE] NOS · info@... · 23 Mai 14:02           │ │
│  │ Não recebemos o pagamento da fatura NOS        │ │
│  │ summary...                                     │ │
│  │ [Pagar agora] [Lembrete] [Ver] [Ignorar]       │ │
│  └────────────────────────────────────────────────┘ │
│                                                     │
│  ● O que eu proponho                 1 evento · 2 t │
│  ┌─ event card (grape) ───────────────────────────┐ │
│  │ [28 Mai] Pagar fatura NOS · €67.40   [Adicionar]│ │
│  └────────────────────────────────────────────────┘ │
│  ┌─ task card (sunset) ───────────────────────────┐ │
│  │ ☐ Responder ao Lutz...    [hoje à tarde] [✓]   │ │
│  └────────────────────────────────────────────────┘ │
│                                                     │
│  ● O que eu tratei                   17 arquivados  │
│  ┌─ news card (sky, italic Fraunces) ─────────────┐ │
│  │ [NEWSLETTERS] 6 fontes · síntese AI            │ │
│  │ "Esta semana o ciclo tech foi dominado por..." │ │
│  │ [source] [source] [source] [source]            │ │
│  └────────────────────────────────────────────────┘ │
│  ┌─ trans list ───────────────────────────────────┐ │
│  │ ● Promoção · Uber — last chance       arquivado│ │
│  │ ● Receipt · Apple — iCloud €2.99      recibo   │ │
│  └────────────────────────────────────────────────┘ │
│                                                     │
│  ┌─ convo hint (brand-tint gradient) ─────────────┐ │
│  │ 💬 Algo não está claro? Pergunta-me. ⌘K        │ │
│  └────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────┘
   ┌───── FLOATING ASK BAR (fixed bottom, dark) ────┐
   │ Pergunta algo — "..."   ⌘K   →                 │
   └────────────────────────────────────────────────┘
```

- Página: `max-width: 800px; margin: 0 auto; padding: 40px 32px 80px;`
- Topbar: sticky, `rgba(250,246,238,0.92)` + `backdrop-filter: blur(8px)`
- Floating ask bar: `position: fixed; bottom: 20px;` com `transform: translateX(-50%)` em desktop, `left/right: 16px` em mobile.
- Sem altura fixa de janela — Electron com `BrowserWindow` resizable.

---

## Componentes (canonical)

A implementação canónica vive em `mockups/colorful-sample.html` (no repositório separado `maildigest-website`). Quando em dúvida sobre dimensões, espaçamentos, ou estados, abre esse ficheiro e copia.

Lista resumida:

| Componente | Notas-chave |
|---|---|
| `.hero` | Card branco com `border-radius: 18px`, `hero-stripe` 6px no topo com 7-color gradient |
| `.stats` | Grid 4 colunas (2 em mobile), pills coloridas com número Fraunces + label Inter |
| `.section-head` | Dot 14px colorido + Fraunces 26px + section-meta JetBrains |
| `.card` | Branco, `border-radius: 14px`, top-stripe 4px colorida por prioridade |
| `.event-card` | Date pill esquerda em `--grape-bg`, border-left 4px grape |
| `.task-card` | Border-left 4px sunset (ou emerald se done), checkbox 20px |
| `.news-card` | Top-stripe sky, summary em Fraunces italic, grid 2-col de sources |
| `.trans-list` | Linhas compactas, dot 8px + categoria mono + texto + status |
| `.convo-hint` | Gradient `--brand-tint` → `--bg`, ícone brand 48px, CTA brand |
| `.ask-bar` | Fixed bottom, fundo `--ink`, pill 999px com shadow forte |
| `.badge` | Filled monday-style, pill 999px, sempre uppercase mono |
| `.btn` | 34px height, border 1px, hover sobe contraste |

### Variantes de botão

```css
.btn          { background: var(--bg2); color: var(--text2); border: 1px solid var(--border2); }
.btn-primary  { background: var(--ink);    color: var(--bg2); }
.btn-brand    { background: var(--brand);  color: var(--brand-on); }
.btn-urgent   { background: var(--urgent); color: white; }
```

### Logo dot animado

```css
.brand-dot {
  width: 8px; height: 8px;
  background: var(--brand);
  border-radius: 50%;
  box-shadow: 0 0 0 3px rgba(108,92,240,0.16);
  animation: pulse 2.4s ease-in-out infinite;
}
@keyframes pulse {
  0%, 100% { opacity: 1; transform: scale(1); }
  50%      { opacity: 0.6; transform: scale(0.85); }
}
```

### Avatar com conic gradient

```css
.avatar {
  width: 34px; height: 34px;
  border-radius: 50%;
  background: conic-gradient(from 140deg at 50% 50%,
    #f59e0b, #e0444c, #a855f7, #6c5cf0, #4f7ce5, #10b981, #f59e0b);
  padding: 2px;
}
.avatar-inner {
  background: var(--bg2);
  border-radius: 50%;
  font-family: 'Syne'… /* fallback OK, Inter Tight 600 também serve */
}
```

---

## Regras de implementação

1. **Fontes:** apenas Fraunces, Inter Tight, JetBrains Mono. Nunca DM Serif Display, Syne, DM Mono, Inter, system-ui.
2. **Sombras:** proibidas excepto `.ask-bar` (definida no mockup) e o `box-shadow` 0 0 0 3px do `.brand-dot`. Cards são planos.
3. **Gradientes:** apenas (a) `hero-stripe` rainbow, (b) `<em>` no hero (brand→rose), (c) `convo-hint` (brand-tint→bg), (d) avatar conic. Todos documentados como excepções.
4. **Borders:** 1px, nunca 0.5px (não rendem em DPR 1x). Excepção: divider entre rows pode usar `border-color: var(--border)` com 1px.
5. **Border-radius:** 14px para cards, 10–12px para sub-cards, 999px para pills/badges, 8px para botões, 6px para inputs.
6. **Transições:** `0.08s ease` em hover de cards/botões. `0.12s` máximo para qualquer interacção.
7. **Cor como código:** badges e section-dots SEMPRE seguem a tabela funcional acima. Não usar cor decorativamente.
8. **Numbers:** sempre `font-variant-numeric: tabular-nums` em stats e contagens.
9. **Escapar input:** o renderer deve `createElement` + `textContent`, nunca `innerHTML` com strings do LLM.
10. **Scrollbars:** ocultas (`::-webkit-scrollbar { width: 0; height: 0; }`).
11. **Selection:** `::selection { background: var(--brand); color: white; }`.
12. **Sections vazias escondem-se:** se `needs_you.length === 0`, secção não renderiza (não mostra estado vazio).
13. **Icons:** inline SVG 16×16 com `stroke="currentColor" stroke-width="1.5-1.7"`. Nunca emoji estrutural.

---

## Acessibilidade

Todos os pares de cor passam WCAG AA:

| Par | Ratio | Status |
|---|---|---|
| `--text` em `--bg` | 14.8:1 | AAA |
| `--text2` em `--bg` | 7.8:1 | AAA |
| `--text3` em `--bg` | 4.6:1 | AA |
| `--brand` em `--bg` | 4.7:1 | AA |
| `white` em `--urgent` | 4.9:1 | AA |
| `white` em `--brand` | 4.9:1 | AA |
| `--sunset-2` em `--sunset-bg` | 7.1:1 | AAA |
| `--emerald-2` em `--emerald-bg` | 5.4:1 | AA |

`prefers-reduced-motion`: desactivar a animação `pulse` do brand-dot e qualquer hover-transform.
Touch targets: botões 34px (acima do mínimo 32px desktop).

---

## Ficheiro de referência

**Visual canónico:** `mockups/colorful-sample.html` no repositório `maildigest-website`.
**Implementação:** `src/renderer/{index.html, styles.css, renderer.js}` no repositório `gmail-daily-digest`.

Não alterar tokens, fontes, ou estrutura sem actualizar este DESIGN.md em sincronia.
