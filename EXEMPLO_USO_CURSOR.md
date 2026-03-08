## Como usar no Cursor

### 1. Instalar o arquivo

Salve o `SKILL.md` dentro do seu projeto ou numa pasta global de skills:

```
seu-projeto/
└── .cursor/
    └── skills/
        └── landing-page-architect/
            └── SKILL.md
```

### 2. Referenciar nas Rules do Cursor

Vá em **Cursor Settings → Rules → Project Rules** e adicione:

```
When the user asks to build a landing page, sales page, or any marketing
page, read the file .cursor/skills/landing-page-architect/SKILL.md and
follow its instructions exactly before writing any code.
```

### 3. Ativar o modelo certo

Em **Cursor Settings → Models**, selecione `claude-opus-4` ou `claude-opus-3-7` como modelo padrão do Agent.

---

## Exemplo de pedido mínimo para usar a skill

Cole isso direto no chat do Cursor Agent:

---

> **Cria uma landing page completa para o seguinte produto:**
>
> **Produto:** Mentoria em grupo "Código que Vende" — programa de 8 semanas para devs freelancers que querem parar de cobrar por hora e passar a vender projetos fechados de R$10k+
>
> **Público:** Desenvolvedor(a) com 2–5 anos de experiência, trabalha como freelancer, cobra entre R$50–R$120/hora, sente que clientes sempre pedem desconto e não valorizam o trabalho técnico
>
> **CTA principal:** Garantir minha vaga (formulário com nome + email, webhook: `https://hook.us1.make.com/abc123`)
>
> **Prova social:** 3 turmas anteriores, 47 alunos formados, depoimentos disponíveis (usar placeholders realistas)
>
> **Urgência real:** Turma abre dia 20/04, máximo 30 vagas
>
> **Preço:** R$1.997 ou 12x R$197
>
> **Tracking:** GTM-XPTO123, Meta Pixel 987654321, GA4 G-ABCD1234
>
> **Tom:** direto, sem enrolação, fala como dev pra dev — zero corporativês
>
> **Cor primária:** #0F172A (dark navy), acento: #6EE7B7 (emerald)
>
> Siga o SKILL.md da landing-page-architect integralmente: entregue o Strategy Brief primeiro, depois o HTML single-file completo com CSS embedded, JS inline, timer de contagem regressiva para 20/04, sticky CTA bar, acordeão de FAQ com 6 objeções reais de dev freelancer, e os blocos de tracking nos lugares certos.

---

Esse pedido já fornece tudo que o **Phase 0** da skill exige de uma vez, então Opus vai direto para o **Strategy Brief** e depois para o HTML — sem ficar pedindo informação em loop.

---

## Exemplo de Pedido Completo — Landing Page Architect Skill

> Cole este prompt inteiro no chat do Cursor Agent com o modelo claude-opus ativo.
> Substitua apenas os valores marcados com 🔁.

---

```
Leia o arquivo .cursor/skills/landing-page-architect/SKILL.md e siga
todas as fases integralmente. Abaixo está o briefing completo do projeto.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 1. PRODUTO / OFERTA
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Nome do produto : Mentoria "Código que Vende"
Tipo            : Programa online em grupo, 8 semanas ao vivo
Formato         : 2 encontros semanais via Zoom + comunidade privada no Discord
Para quem       : Devs freelancers com 2–5 anos de experiência que cobram por
                  hora e querem migrar para projetos fechados de alto valor
Transformação   : Sair de R$60/hora para fechar projetos de R$10k–R$30k com
                  método, contrato e posicionamento profissional
Preço           : R$1.997 à vista ou 12x de R$197
Bônus incluídos :
  - Template de proposta comercial (Google Docs editável)
  - Planilha de precificação de projetos
  - Banco de 40 scripts de resposta para objeções de cliente
  - Acesso vitalício às gravações
Garantia        : 30 dias — devolução integral sem perguntas
Turma           : Abre 20/04/2025, máximo 30 vagas, 11 já preenchidas


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 2. PÚBLICO-ALVO
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Perfil          : Desenvolvedor(a) freelancer, 25–38 anos, Brasil
Dores principais:
  - Cliente sempre pede desconto e compara com "meu sobrinho que faz mais barato"
  - Não sabe como justificar preço sem virar leilão
  - Medo de mandar proposta e nunca mais ouvir resposta
  - Renda instável — mês bom, mês ruim, zero previsibilidade
  - Sente que trabalha muito e ganha pouco comparado ao esforço técnico
Objeções reais  :
  - "Não tenho tempo agora, projeto acumulando"
  - "Já tentei curso de vendas e não funcionou pra dev"
  - "R$2k é muito, não sei se vou recuperar"
  - "Tenho medo de parecer vendedor chato"
  - "E se eu não conseguir aplicar sozinho?"
  - "Meu nicho é pequeno, não tem cliente disposto a pagar isso"
Tom de voz      : Direto, sem enrolação, fala como dev pra dev — zero
                  corporativês, zero coach motivacional


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 3. TEXTOS PRONTOS (usar verbatim no HTML)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

### Headline principal
"Pare de cobrar por hora. Comece a fechar projetos."

### Subheadline
"O método que 47 devs freelancers usaram para parar de negociar desconto
e passar a vender projetos fechados de R$10k a R$30k — sem virar vendedor."

### CTA primário (botão)
"Quero garantir minha vaga"

### CTA secundário (âncora para formulário, usado em seções intermediárias)
"Garantir minha vaga agora"

### Microcopy abaixo do CTA
"🔒 Pagamento seguro · Garantia de 30 dias · Apenas 19 vagas restantes"

### Headline seção Problema
"Você é bom no que faz. O problema não é a sua skill técnica."

### Headline seção Solução
"A diferença entre R$60/hora e R$15k por projeto é uma só: o método."

### Headline seção Benefícios
"O que você vai conquistar nas 8 semanas"

### Benefícios (usar exatamente estes, nesta ordem)
- **Precificação sem achismo** — Fórmula exata para calcular o valor do
  seu projeto com base em entrega, não em horas gastas.
- **Proposta que fecha sozinha** — Template testado que transforma
  "vou pensar" em "quando a gente começa?".
- **Objeção virou conversa** — 40 scripts para responder cliente que
  pede desconto sem perder a venda nem a dignidade.
- **Posicionamento que atrai** — Como reescrever seu perfil e LinkedIn
  para o cliente certo te achar (e já chegar disposto a pagar).
- **Renda previsível** — Sistema de pipeline simples que você mantém
  em 30 minutos por semana.
- **Contratos sem susto** — Modelo jurídico validado por advogado que
  protege você e passa confiança pro cliente.

### Headline seção Como Funciona
"Como as 8 semanas funcionam"

### Passos (3 etapas)
1. **Fundação** (semanas 1–2): Posicionamento, precificação e proposta.
   Você sai sabendo exatamente quanto cobrar e como apresentar.
2. **Conversão** (semanas 3–5): Scripts de venda, tratamento de
   objeções e fechamento. Você sai com playbook de vendas na mão.
3. **Escala** (semanas 6–8): Pipeline, follow-up e como manter
   R$15k+ de receita mensal de forma consistente.

### Headline seção Depoimentos
"O que dizem os devs da turma anterior"

### Depoimentos (3 principais)
1. "Fechei um projeto de R$18.500 na terceira semana de mentoria.
   Antes eu teria cobrado R$4k pelo mesmo escopo com medo de perder o cliente."
   — João Mendes, Dev Full Stack, São Paulo

2. "A parte de objeções mudou tudo. Hoje quando cliente fala em
   desconto eu nem fico nervoso — é só seguir o script."
   — Ana Rodrigues, Dev React, Curitiba

3. "Fiz R$32k nos dois meses seguintes à mentoria. Não é sorte,
   é método. Recomendo sem hesitar."
   — Carlos Lima, Dev Backend Node.js, Recife

### Depoimento destaque (case study — seção antes do CTA final)
"Em 60 dias fechei R$47.200 em projetos. Minha hora estava em R$80.
Hoje não falo mais em hora — vendo resultado."
— Thiago Alves, Dev Fullstack, Florianópolis | Turma 2 | Resultado em 60 dias

### Headline seção Oferta / Value Stack
"Tudo que você recebe ao garantir sua vaga"

### Headline seção Garantia
"Risco zero. Garantia incondicional de 30 dias."

### Texto garantia
"Se em 30 dias você não sentir que o método funciona para o seu contexto,
basta mandar um e-mail. Devolvemos 100% do valor sem perguntas,
sem burocracia, sem precisar justificar nada."

### Headline FAQ
"Respostas para o que você está pensando agora"

### Headline seção Final CTA
"19 vagas. Turma abre em 20/04. Depois disso, lista de espera."

### Texto urgência final
"Cada turma tem máximo de 30 alunos para garantir atenção individual
nos encontros ao vivo. 11 vagas já foram preenchidas."

### Headline rodapé
"Código que Vende — Mentoria para Devs Freelancers"

### Tagline rodapé
"Pare de negociar. Comece a fechar."


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 4. IMAGENS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Logo principal    : /assets/images/logo.svg
                    (versão light para fundo escuro no header)
Logo rodapé       : /assets/images/logo-white.svg

Hero background   : /assets/images/hero-bg.jpg
                    Descrição: dev masculino, 30 anos aprox, setup dual monitor,
                    ambiente escuro com luz azul, expressão confiante
                    Dimensões esperadas: 1440×900px

Foto do mentor    : /assets/images/mentor.jpg
                    Descrição: Rafael Costa, fundador, terno casual,
                    fundo desfocado, expressão acessível
                    Usar na seção "Quem criou este método"
                    Dimensões: 600×600px, recortada em círculo via CSS

Avatares depoimentos:
  João Mendes    : /assets/avatars/joao.jpg   (400×400px)
  Ana Rodrigues  : /assets/avatars/ana.jpg    (400×400px)
  Carlos Lima    : /assets/avatars/carlos.jpg (400×400px)
  Thiago Alves   : /assets/avatars/thiago.jpg (400×400px)

Ícones de benefícios (SVG inline, não usar img tag):
  - Precificação : ícone de calculadora estilizada
  - Proposta     : ícone de documento com check
  - Objeções     : ícone de chat com escudo
  - Posicionamento: ícone de alvo / target
  - Renda        : ícone de gráfico crescente
  - Contratos    : ícone de documento assinado

Badge garantia    : /assets/images/badge-garantia-30.svg
                    (usar na seção de garantia e próximo ao CTA final)

Logos de mídia (prova social de autoridade, linha horizontal):
  /assets/logos/techmundo.svg
  /assets/logos/tableless.svg
  /assets/logos/dev-to.svg
  /assets/logos/twitter-x.svg
  Legenda: "Falaram sobre o método"

OG Image (meta tag):/assets/images/og-cover.jpg  (1200×630px)
Favicon           : /assets/favicon.ico


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 5. VÍDEOS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

VSL (Video Sales Letter) — seção Hero:
  Embed YouTube  : https://www.youtube.com/embed/ID_DO_VIDEO_VSL
  Posição        : Logo abaixo do subheadline, antes do CTA
  Comportamento  : Clique para play (não autoplay), 16:9, lazy load
  Thumbnail      : /assets/images/vsl-thumb.jpg
  Legenda abaixo : "▶ Assista: Como devs comuns estão fechando projetos
                    de R$10k+ (11 min)"

Vídeo depoimento — seção Social Proof:
  Embed YouTube  : https://www.youtube.com/embed/ID_DO_VIDEO_DEPO
  Posição        : Após os 3 depoimentos em cards
  Comportamento  : Clique para play, 16:9
  Legenda        : "Thiago conta como fez R$47k em 60 dias"

Loop background (opcional, seção How it Works):
  Arquivo        : /assets/video/dashboard-loop.mp4
  Comportamento  : autoplay, muted, loop, playsinline, sem controles
  Fallback       : /assets/images/dashboard-fallback.jpg
  Posição        : Background da seção "Como funciona", opacidade 15%


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 6. FORMULÁRIO & INTEGRAÇÃO
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Campos            : nome completo + e-mail + WhatsApp (com máscara)
Webhook POST      : https://hook.us1.make.com/abc123xyz
Headers           : Content-Type: application/json
Payload esperado  :
  {
    "name":      "...",
    "email":     "...",
    "whatsapp":  "...",
    "source":    "landing-page",
    "utm_source": window.utm_source || "",
    "utm_medium": window.utm_medium || "",
    "utm_campaign": window.utm_campaign || ""
  }

Mensagem sucesso  : "✅ Vaga reservada! Verifique seu e-mail em até 5 minutos."
Mensagem erro     : "Algo deu errado. Tente novamente ou fale no WhatsApp."
Redirect pós-form : Não redirecionar — trocar o form pelo success state inline.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 7. ANALYTICS & TRACKING
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Google Tag Manager : GTM-XPTO123
Google Analytics 4 : G-ABCD1234
Meta Pixel ID      : 987654321098765

Eventos a disparar:
  - Pageview       : automático via GTM
  - CTA click      : gtag event "cta_click" + fbq "ViewContent"
                     em todos os botões CTA da página
  - Form submit    : gtag event "generate_lead" + fbq "Lead"
  - Video play VSL : gtag event "video_play" { video_title: "VSL Hero" }
  - Scroll 50%     : gtag event "scroll_depth" { depth: 50 }
  - Scroll 90%     : gtag event "scroll_depth" { depth: 90 }
  - Timer visible  : gtag event "urgency_viewed"


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 8. URGÊNCIA & ESCASSEZ
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Countdown target  : 2025-04-20T23:59:59-03:00
Formato exibição  : DD dias  HH horas  MM min  SS seg
Posição           : Abaixo do hero CTA + no topo da seção Final CTA
Comportamento ao zerar: substituir por "Inscrições encerradas.
                         Entre na lista de espera."

Vagas restantes   : Exibir "Apenas 19 vagas restantes" como badge
                    vermelho pulsante próximo ao CTA principal
                    (número fixo — não precisa ser dinâmico)


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 9. REDES SOCIAIS & LINKS EXTERNOS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Instagram         : https://instagram.com/codigoquevende
YouTube           : https://youtube.com/@codigoquevende
LinkedIn          : https://linkedin.com/company/codigoquevende
WhatsApp suporte  : https://wa.me/5511912345678
                    Texto: "Falar com o time antes de entrar"

Email contato     : contato@codigoquevende.com.br
Política privacidade: /privacidade
Termos de uso     : /termos

Todos os links externos: target="_blank" rel="noopener noreferrer"
Links de redes sociais: exibir apenas no footer, ícones SVG inline,
                         hover com cor da rede social respectiva


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 10. DESIGN SYSTEM
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Modo              : Dark (fundo escuro, texto claro)
Cor primária      : #0F172A  (navy escuro — fundo principal)
Cor superfície    : #1E293B  (cards e seções alternadas)
Cor acento / CTA  : #6EE7B7  (emerald — botões, destaques)
Cor texto         : #F1F5F9  (quase branco)
Cor texto muted   : #94A3B8  (cinza azulado)
Cor borda         : #334155  (separadores sutis)
Cor erro          : #F87171  (validação de form)
Cor sucesso       : #34D399  (estados de sucesso)

Fonte display     : "Syne" (Google Fonts) — headlines
Fonte corpo       : "DM Sans" (Google Fonts) — body, UI

Raio de borda     : 12px cards, 8px botões, 6px inputs
Sombra cards      : 0 4px 24px rgba(0,0,0,0.4)
Gradiente hero    : linear-gradient(135deg, #0F172A 0%, #1E3A5F 100%)
Gradiente CTA btn : linear-gradient(135deg, #6EE7B7, #3B82F6)


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 11. SEO
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Title             : Mentoria para Dev Freelancer — Feche Projetos de R$10k+ | Código que Vende
Meta description  : Aprenda a precificar e fechar projetos de alto valor sem
                    negociar desconto. Método usado por 47 devs. Turma abre
                    20/04 — apenas 30 vagas.
URL canônica      : https://codigoquevende.com.br/mentoria
lang              : pt-BR
Schema type       : Course
  name            : Mentoria Código que Vende
  provider        : Código que Vende
  price           : 1997
  priceCurrency   : BRL
  startDate       : 2025-04-20


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 12. COMPORTAMENTOS ESPECIAIS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Sticky bar        : Ativar após 600px de scroll
                    Texto: "Turma abre 20/04 · 19 vagas"
                    Botão: "Garantir minha vaga" (âncora para form)
                    Ocultar quando o hero CTA estiver visível
                    (usar IntersectionObserver)

Smooth scroll     : Todos os links âncora (#form, #como-funciona etc)
                    com behavior: smooth

UTM params        : Capturar utm_source, utm_medium, utm_campaign da URL
                    e incluir no payload do form automaticamente

Exit intent       : Não implementar — aumenta bounce sem ganho real neste
                    contexto

Acessibilidade    : aria-label em todos os ícones SVG standalone
                    focus-visible em todos os elementos interativos
                    skip-to-content link no início do body
                    prefers-reduced-motion: desativar animações se ativo


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 13. PLACEHOLDERS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Para qualquer asset não listado acima:
  - Imagens    : <div class="img-placeholder"> com dimensões corretas
                 e comentário <!-- SUBSTITUIR: descrição -->
  - Textos     : [TEXTO A DEFINIR] em maiúsculas dentro de colchetes
  - URLs       : href="#placeholder" com comentário <!-- URL: descrição -->
  - Vídeos     : thumbnail clicável com ícone play SVG e comentário
                 <!-- SUBSTITUIR: URL do vídeo -->
```

---

## O que este pedido cobre

| Categoria | Itens fornecidos |
|-----------|-----------------|
| Produto | Descrição, formato, bônus, preço, garantia, vagas |
| Público | Perfil, dores, objeções, tom de voz |
| Textos | Headlines, subheadlines, CTAs, microcopy, benefícios, depoimentos, FAQ hints |
| Imagens | Logo, hero, mentor, avatares, ícones, badges, logos mídia, OG, favicon |
| Vídeos | VSL embed, depoimento embed, loop background |
| Formulário | Campos, webhook, payload, UTMs, estados de sucesso/erro |
| Analytics | GTM, GA4, Meta Pixel, 6 eventos customizados |
| Urgência | Countdown com data real, vagas restantes, comportamento ao zerar |
| Social | Instagram, YouTube, LinkedIn, WhatsApp, email, links legais |
| Design | Paleta dark completa, fontes, raios, sombras, gradientes |
| SEO | Title, description, canonical, lang, Schema Course |
| Comportamentos | Sticky bar, smooth scroll, UTM capture, acessibilidade, reduced-motion |
| Placeholders | Regra clara para tudo que não foi fornecido |