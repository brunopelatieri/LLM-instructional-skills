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

## Exemplo de pedido para usar a skill

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