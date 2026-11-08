# Guia Completo: Sites Estilo MotionSitesAI do Zero

> Pesquisa avançada baseada em: (1) análise real do site do criador do TikTok @victorsyrion ("Bueno | Coder" / Code Makers), com o bundle JS inspecionado ao vivo; (2) documentação e lessons oficiais do MotionSites.ai; (3) skills de motion abertas no GitHub (MengTo Skills, claude-skills, awesome-copilot premium-frontend-ui).

---

## 1. O que é um "Motion Site"?

Um **motion site** é um site projetado para se mover: animações de entrada, scroll cinematográfico, parallax, reveals de texto, hover magnético, cursor customizado e backgrounds animados. Não é site "com um efeito", é **coreografia** — toda a página como uma sequência coordenada.

O MotionSites.ai (motionsites.ai) é uma biblioteca de **prompts premium** que descrevem designs prontos em nível de código: layout, tipografia, cores, animações, responsividade, dependências. O fluxo deles é:

```
Escolher design → Copiar prompt completo → Colar no editor de IA
(Cursor / Claude Code / Lovable / Bolt / v0) → Gerar → Refinar → Publicar
```

Segundo o próprio site: 500+ prompts, 50.000+ sites construídos, 100.000 usuários. A tese central deles: *"Motion alone isn't enough. It still needs good typography, spacing, structure, and restraint."* (Movimento sozinho não basta — precisa de tipografia boa, espaçamento, estrutura e contenção.)

---

## 2. Análise real: como o site do @victorsyrion (Code Makers) é feito

Perfil analisado ao vivo: TikTok @victorsyrion → bio.codemakers.com.br → codemakers.com.br

**Stack confirmada inspecionando o bundle JS (`index-ByRi5w4V.js`, 651 KB):**

| Tecnologia | Papel | Evidência |
|---|---|---|
| **React + Vite** | SPA base | `#root` react root, bundle Vite com hash |
| **Tailwind CSS** | Estilo | CSS compilado via assets |
| **GSAP** (55 ocorrências) | Motor de animação | Tween/coreografia de entrada e scroll |
| **GSAP ScrollTrigger** (5 ocorrências) | Animações atreladas ao scroll | Reveals por seção |
| **Lenis** (29 ocorrências) | Smooth scroll (inércia) | O "pulo do gato" do feel premium |
| **Framer Motion** | Animações React | Menor presença, complementa GSAP |
| **lucide-react** | Ícones | Ícones de seta ↗ em todos CTAs |

**Padrões de copy/estrutura que ele usa (repertório de conversão):**
- Hero com frases quebradas em linhas: "Crie com IA. / Encontre com a Code Hunter. / Venda com a nossa metodologia."
- Eyebrow labels em caps: "CODE MAKERS + CODE HUNTER", "DA IDEIA AO DINHEIRO NO BOLSO"
- Seções numeradas: 01 CRIAR → 02 ENCONTRAR → 03 VENDER (processo em 3 passos)
- CTAs com seta ↗ e hover animado
- Bilinguismo visual: PT para copy, inglês para labels técnicos

**Conclusão prática:** o "site caro" que ele vende não é tecnologia exótica. É React + Tailwind + GSAP + ScrollTrigger + Lenis, com contenção de design. Isso é exatamente o stack que os prompts do MotionSites geram — e é 100% replicável do zero com IA.

---

## 3. Stack do zero (a receita)

```bash
npm create vite@latest meu-site -- --template react-ts
cd meu-site
npm i gsap @gsap/react lenis framer-motion lucide-react
npm i -D tailwindcss @tailwindcss/vite
```

**Por que cada peça:**
- **GSAP + ScrollTrigger** — animações de entrada, scroll reveals, seções pinadas, timelines coreografadas.
- **Lenis** — smooth scroll com inércia. É a lib atrás da maioria dos sites da Awwwards. Sem ele, o scroll "pula"; com ele, as animações lêem como um movimento contínuo.
- **Framer Motion** — animações declarativas em componentes React (menus, modais, AnimatePresence).
- **Tailwind** — velocidade de estilização + consistência de tokens.

---

## 4. O código base (motion system) — copie e reutilize em todo projeto

Este é o "motor" que todo motion site premium compartilha (fonte: skill *cinematic-gsap-lenis-motion-system*).

**`src/lib/motion.ts` — inicialização única:**

```js
import Lenis from "lenis";
import "lenis/dist/lenis.css";
import { gsap } from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";

gsap.registerPlugin(ScrollTrigger);
gsap.defaults({ ease: "power3.out", duration: 0.85 });

const reduceMotion = window.matchMedia("(prefers-reduced-motion: reduce)").matches;

let lenis;
if (!reduceMotion) {
  lenis = new Lenis({ lerp: 0.08, smoothWheel: true, wheelMultiplier: 0.9, anchors: true });
  lenis.on("scroll", ScrollTrigger.update);
  gsap.ticker.add((time) => { lenis.raf(time * 1000); });
  gsap.ticker.lagSmoothing(0);
}

window.addEventListener("load", () => ScrollTrigger.refresh());
```

**Tokens de movimento (a "gramática" do feel premium):**

| Elemento | Valor |
|---|---|
| Eases | `power3.out`, `power4.out`, `expo.out` |
| Scroll scrub | `scrub: 0.8` a `1.4` (atraso cinematográfico) |
| Reveals | 0.75s a 1.1s |
| Hover | 0.35s a 0.6s |
| Cursor lag | 0.25s a 0.45s |
| Stagger de texto (palavras) | 0.035s a 0.07s |
| Stagger de linhas | 0.08s a 0.14s |
| Stagger de cards | 0.06s a 0.1s |
| Gatilho de reveal | `start: "top 82%"` |

**Regra de ouro:** animate APENAS `transform`, `opacity` e `clip-path` curto. Evite bounce/elastic/spring. Prefira sutileza: movimento deve guiar a leitura, não competir com ela.

**Coreografia padrão:**
- Hero: background/mídia entra primeiro → headline em linhas → copy de apoio → CTA por último.
- Seções: label (eyebrow) → título → mídia → cards/detalhes.
- Parallax: background mais lento, foreground levemente mais rápido, texto estável.
- Hover magnético: só `x/y`, scale sob 1.03; desabilitar em touch (`pointer: coarse`).
- `prefers-reduced-motion`: sempre respeitar — conteúdo estático, sem sequestro de scroll.

**QA obrigatório:** conteúdo visível sem JS; reveals disparam 1x; seções pinadas não sobrepõem a próxima; cursor custom escondido em mobile; `ScrollTrigger.refresh()` após fontes/imagens carregarem.

---

## 5. O fluxo passo a passo completo (do zero ao cliente)

### Passo 1 — Referência e briefing (não pule isso)
- Junte 3–5 referências (Awwwards, Godly, Land-book, ou os previews do MotionSites).
- Defina: paleta (2 cores + neutros), 1 tipografia display + 1 de texto, mood (dark editorial? claro e clean?).
- Escreva o copy REAL antes do código. Motion site sem copy definido vira "AI slop".

### Passo 2 — Setup do projeto
```bash
npm create vite@latest cliente-site -- --template react-ts
npm i gsap @gsap/react lenis framer-motion lucide-react
```
Cole o motion system da Seção 4. Estrutura:
```
src/
  lib/motion.ts          ← setup Lenis + GSAP
  components/            ← Navbar, Hero, Section, Cards, Footer
  hooks/useReveal.ts     ← helper de reveal
  App.tsx
```

### Passo 3 — Gere seção por seção com prompts de IA
**Nunca peça "o site inteiro" de uma vez.** Um prompt por seção, na ordem. (Prompts prontos na Seção 6.)

### Passo 4 — Refino (instruções pequenas e específicas)
O tutorial oficial do MotionSites recomenda exatamente isto:
- "Reduza o tamanho do headline em 15%."
- "Escureça o vídeo de fundo para o texto ficar legível."
- "Melhore o espaçamento entre o hero e a barra inferior."
- "Deixe a animação do menu mobile mais suave."

Instruções pequenas >>> "redesenhe a página".

### Passo 5 — Teste e publique
- Teste mobile em TODAS as seções (motion sites quebram mais no mobile).
- Lighthouse > 90 (prefers-reduced-motion ajuda).
- Deploy: **Vercel** (conecta direto ao GitHub, grátis) ou Cloudflare Pages.
- Vídeo hero: comprima para < 4 MB (screencast.com / Cloudflare Stream).

---

## 6. Biblioteca de prompts prontos (copie e cole)

### PROMPT 0 — Diretor de arte (rode primeiro, um por projeto)
```
Você é um diretor de arte web de nível Awwwards. Crie o design system para o site de [NEGÓCIO], que vende [PRODUTO/SERVIÇO] para [PÚBLICO].

Defina em lista objetiva:
1. Mood (3 adjetivos) e referência visual
2. Paleta: 1 cor primária, 1 cor de destaque, fundo, texto (hex exatos)
3. Tipografia: 1 fonte display (Google Fonts) + 1 fonte de texto, pesos, escala fluida (clamp)
4. Estilo de motion: tipos de reveal, velocidade, intensidade
5. Estrutura de seções: hero → prova → processo → oferta → FAQ → CTA, com o copy de CADA seção já escrito em pt-BR

Não escreva código. Só a spec.
```

### PROMPT 1 — Hero cinematográfico (o mais importante)
```
Crie o HERO de uma landing page para [NEGÓCIO]. Stack: Vite + React + TypeScript + Tailwind CSS + GSAP (ScrollTrigger) + Lenis + lucide-react.

ESPECIFICAÇÃO VISUAL:
- Full-screen (100vh), fundo [cor/vídeo/imagem com overlay rgba(0,0,0,0.4)]
- Headline em 3 linhas quebradas manualmente, fonte display, peso 600,
  font-size: clamp(3rem, 8vw, 8rem), line-height 0.95, tracking -0.03em
- Eyebrow em caps acima do headline: font-size 12px, letter-spacing 0.2em, opacity 0.7
- Subheadline de apoio: max-width 32rem, cor rgba(255,255,255,0.7)
- CTA: pill com fundo claro, texto escuro, ícone ArrowUpRight do lucide, hover scale 1.05
- Badge/meta no rodapé do hero: "© 2026 · [cidade] · disponível para novos projetos"

ANIMAÇÃO (GSAP timeline na entrada, coreografada nesta ordem):
1. Overlay de preloader some (clip-path inset de 100% para 0, 0.8s, power4.inOut)
2. Headline: cada linha sobe de yPercent:110 para 0 dentro de máscara overflow-hidden,
   stagger 0.11s, duration 1s, ease power4.out
3. Eyebrow e subheadline: fade-up 20px, stagger 0.08s
4. CTA: scale 0.9 → 1 + fade
5. Background: scale 1.15 → 1, duration 1.6s

RESPONSIVO: mobile = headline clamp(2.5rem,11vw,4rem), CTA full-width, sem parallax em touch.
ACESSEBILIDADE: prefers-reduced-motion desativa tudo (estado final visível).
```

### PROMPT 2 — Scroll reveal de seção
```
Adicione uma seção [TÍTULO] com scroll reveal usando GSAP ScrollTrigger:
- Eyebrow caps → headline em 2 linhas → parágrafo → grid de [N] cards
- Cada card: borda 1px rgba(255,255,255,0.08), radius 16px, padding 32px,
  número "01/02/03" pequeno no topo, título, descrição
- Reveal: trigger "top 82%", cards entram de y:36 + opacity:0 + blur(8px) → 0/1/0,
  stagger 0.075s, duration 0.95s, ease power4.out, once: true
- Nunca anime propriedades de layout; só transform/opacity/filter
```

### PROMPT 3 — Parallax + imagem clip reveal
```
Crie uma seção de imagem com:
- Container overflow-hidden com clip-path: inset(0 0 100% 0) inicial
- Ao entrar no viewport (start "top 82%"): clip-path anima para inset(0 0 0% 0),
  duration 1.1s, power4.out; a imagem dentro vai de scale 1.08 para 1
- Parallax sutil no scroll: imagem desloca y de -18% com scrub 1.2 (invalidateOnRefresh: true)
```

### PROMPT 4 — Seção pinada (storytelling)
```
Crie uma seção pinada estilo storytelling com GSAP ScrollTrigger:
- section com pin: true, anticipatePin: 1, end: "+=" + (número de painéis * 100vh), scrub 1.1
- 3 painéis full-screen que sobem em sequência (yPercent -100)
- Camada de fundo (data-story-depth) sobe a -16% criando profundidade
- Uma ideia por viewport. Mobile: desativar pin, empilhar painéis normalmente (gsap.matchMedia)
```

### PROMPT 5 — Detalhes premium (magnetic + cursor)
```
Adicione os detalhes de nível Awwwards:
1. BOTÕES MAGNÉTICOS: data-magnetic com força 0.18, gsap.quickTo x/y (duration 0.45,
   power3.out), pointermove no botão, retorno a 0 no pointerleave. Scale máx 1.03.
   Desabilitar em (pointer: coarse).
2. CURSOR CUSTOM: div fixo, mix-blend-mode: difference, segue mouse com quickTo
   (duration 0.35). Ao hover em [data-cursor-label="Ver"]: scale 1.75 e mostra o label.
   Esconder em touch e prefers-reduced-motion.
3. MARQUEE: faixa infinita de texto em caps com scroll suave, pausa no hover.
```

### PROMPT 6 — Menu mobile animado (Framer Motion)
```
Menu mobile fullscreen com Framer Motion AnimatePresence:
- Hamburger 36x36, 3 barras 2px/20px; ao abrir: barra 1 gira 45°, barra 2 some,
  barra 3 gira -45° (duration 0.3, ease [0.25,0.1,0.25,1])
- Overlay fixed inset-0, z-55, bg rgba(2,3,25,0.95) + backdrop-blur 24px
- Links centralizados, 30px, stagger de entrada {opacity:0,y:20,blur(4px)} → visível,
  stagger 0.06s, duration 0.35; saída inverte a ordem
- Travar o scroll do body (e pausar o Lenis) enquanto aberto
```

### PROMPT 7 — Prompt de ADAPTAÇÃO para cliente (pós-geração)
```
Mantenha o layout, tipografia, cores, animações e comportamento responsivo atuais.
Reescreva o conteúdo para o negócio [X]: adicione serviços, cases, FAQ e CTA final.
Substitua imagens genéricas por [fotos do cliente / screenshots / assets gerados por IA].
Não mude nada de animação.
```

---

## 7. Erros que denunciam "site de IA" (e como evitar)

1. **Motion sem contenção** — 10 animações competindo. Regra: 1 ideia por viewport.
2. **Texto quebrando em qualquer lugar** — quebre linhas manualmente no headline.
3. **Animação de propriedades de layout** (width/height/top) — só transform/opacity/clip-path.
4. **Lenis sem sincronizar com ScrollTrigger** — copie o código da Seção 4 exatamente (lenis.on("scroll", ScrollTrigger.update) + ticker do GSAP). Um único dono de RAF.
5. **Esquecer prefers-reduced-motion e mobile** — metade dos visitantes é mobile; cursor custom e magnetic off em touch.
6. **Copy genérico** — "Soluções inovadoras para o seu negócio" mata qualquer site premium. Copy específico > adjetivo.
7. **Não dar ScrollTrigger.refresh()** após fontes/imagens carregarem — reveals disparando no lugar errado.
8. **Vídeo de fundo pesado** — comprimir agressivamente; se > 4 MB, troque por imagem + gradient animado.

---

## 8. Ferramentas do ecossistema

| Ferramenta | Uso |
|---|---|
| **MotionSites.ai** | Biblioteca de prompts de designs prontos (pago) |
| **Cursor / Claude Code** | Editor de IA para rodar os prompts |
| **Lovable / Bolt / v0** | Alternativas no-browser |
| **MengTo Skills (GitHub)** | Skills gratuitas de motion/web design para agentes de IA |
| **GSAP docs (gsap.com/docs)** | ScrollTrigger, timelines |
| **Lenis (darkroom.engineering/lenis)** | Smooth scroll |
| **Awwwards / Godly / Land-book** | Referência visual |
| **Vercel / Cloudflare Pages** | Deploy gratuito |
| **Relume / Figma First** | Wireframe rápido antes do código |

---

## 9. Checklist de entrega (use em cada projeto)

- [ ] Design system definido antes do código (PROMPT 0)
- [ ] Motion system base instalado (Seção 4)
- [ ] Hero coreografado (ordem: bg → headline → copy → CTA)
- [ ] Reveals com trigger "top 82%", once
- [ ] Seção pinada? Máximo 1–2 por página
- [ ] Magnetic + cursor desativados em touch
- [ ] prefers-reduced-motion respeitado
- [ ] Testado em mobile (todas as seções)
- [ ] Lighthouse > 90
- [ ] Deploy na Vercel + domínio do cliente

---

*Fontes principais: motionsites.ai (lessons oficiais), skill cinematic-gsap-lenis-motion-system (GitHub), análise ao vivo do bundle de codemakers.com.br (@victorsyrion), gsap.com/docs, madeforaward.com, awesome-copilot premium-frontend-ui skill.*
