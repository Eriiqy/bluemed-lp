# CLAUDE.md — Briefing pro Claude do parceiro

> Este arquivo é o ponto de entrada pra qualquer Claude que abrir este repo.
> Leia tudo antes de editar.

## O que é este projeto

Landing page estática do **Plano Sênior Blue Med Saúde** (Baixada Santista).
Cliente final: Paulo Flor (corretor Blue Med / Trasmontano).

- **Stack:** HTML + CSS + JS puro, single-file. Tudo está em `index.html`.
- **Sem build, sem framework, sem npm.** Editar é só abrir o arquivo.
- **Deploy:** Vercel, automático em todo push pra `main`.
- **Domínio atual:** definido no painel da Vercel (perguntar ao dono do repo).

## Tarefas pendentes pro parceiro

Três coisas precisam ser configuradas:

1. **Pixel Meta (Facebook/Instagram Ads)** — base code + evento Lead
2. **GA4 (Google Analytics 4)** — base code + evento generate_lead
3. **Google Ads Conversion** — tag de conversão no clique do WhatsApp / submit do form
4. **Domínio** — apontar DNS pra Vercel

A boa: o "esqueleto" de tracking já está pronto. Você só precisa **(a)** colar os scripts base no `<head>` e **(b)** descomentar/preencher 3 linhas no helper de tracking.

---

## 1) Onde colar os pixels base

Abra `index.html` e procure pelo marcador:

```
PARTNER: PIXELS / ANALYTICS GO HERE
```

Está logo antes de `</head>` (próximo da linha 1075). Cole os 3 scripts ali, nesta ordem:

### a) Meta Pixel
Obter em: https://business.facebook.com/events_manager
Cole o snippet base (o que tem `fbq('init', 'SEU_PIXEL_ID')` + `fbq('track', 'PageView')`).

### b) GA4 (gtag.js)
Obter em: https://analytics.google.com → Admin → Streams de dados → Web
Cole o snippet base (o que tem `gtag('config', 'G-XXXXXXX')`).

### c) Google Ads Conversion
Obter em: https://ads.google.com → Ferramentas e configurações → Conversões
Cole o snippet base do Google Ads (geralmente `gtag('config', 'AW-XXXXXXX')`).
**Anote o `send_to` da ação de conversão** (formato `AW-XXXXXX/AbCdEfGhIj`) — vai usar no passo 2.

---

## 2) Ativar os eventos de conversão

Os eventos JÁ estão wired em dois lugares:
- **Todo clique em `wa.me/*`** (delegação de evento, pega os 8+ links automaticamente)
- **Submit do formulário modal** (`#planForm` em `index.html`)

Procure no `index.html` pelo segundo marcador (perto do fim do `<body>`):

```
PARTNER: CONVERSION TRACKING
```

Você verá a função `window.trackWhatsAppLead`. Dentro dela tem 3 linhas comentadas — **descomente** após colar os pixels:

```js
// --- Meta Pixel ---
if (typeof fbq === 'function') fbq('track', 'Lead', { source: source });

// --- GA4 ---
if (typeof gtag === 'function') gtag('event', 'generate_lead', { source: source });

// --- Google Ads Conversion ---
if (typeof gtag === 'function') gtag('event', 'conversion', {
  send_to: 'AW-CONVERSION_ID/CONVERSION_LABEL'  // <-- substituir
});
```

**Troque `AW-CONVERSION_ID/CONVERSION_LABEL`** pelo `send_to` da etapa 1.c.

Não precisa mexer em mais nada do HTML. Os links de WhatsApp e o form já chamam esse helper sozinhos.

---

## 3) Validação

### Localmente
```bash
python3 -m http.server 8765
# abrir http://localhost:8765
```

### Meta Pixel
- Instale a extensão **Meta Pixel Helper** no Chrome.
- Abrir a página → deve mostrar 1 pixel ativo, evento `PageView`.
- Clicar em qualquer botão de WhatsApp → deve disparar evento `Lead`.
- Abrir o modal e submeter o form → outro `Lead`.

### GA4
- Vá em **GA4 → Admin → DebugView**.
- Na página, abrir DevTools e rodar: `window.gtag('config','G-XXX',{debug_mode:true})` (ou usar a ext. Google Tag Assistant Companion).
- Clicar no WhatsApp → evento `generate_lead` deve aparecer em ~5s no DebugView.

### Google Ads
- **Google Tag Assistant** (https://tagassistant.google.com) → "Add domain" → seu site.
- Clicar no WhatsApp → o conversion event deve aparecer como disparado.
- A conversão real só conta depois de ser puxada via Google Ads (até 24h).

### Console
Em qualquer caso, o `console.log('[LEAD]', source)` continua disparando — útil pra debug rápido sem extensão.

---

## 4) Deploy

```bash
git add index.html
git commit -m "feat: add Meta Pixel + GA4 + Google Ads conversion tracking"
git push origin main
```

Vercel faz o redeploy em ~30s. Verifique em https://vercel.com (login do dono do repo).

---

## 5) Domínio

A LP roda hoje no domínio padrão `.vercel.app` (ou o domínio que já estiver apontado).

Pra trocar/adicionar domínio:
1. Vercel Dashboard → este projeto → **Settings → Domains**
2. Adicionar o domínio
3. Vercel mostra os registros DNS necessários (geralmente um `A` ou `CNAME`)
4. Configurar no painel do registrador (Registro.br, GoDaddy, etc.)
5. Aguardar propagação (15min a algumas horas)

**Atenção:** Se trocar de domínio, lembrar de:
- Atualizar a **Allowed Domains** no Pixel Meta (Business Manager)
- Atualizar a propriedade no GA4 se mudar de URL principal
- Re-adicionar o domínio no Google Tag Assistant pra validar

---

## Regras de ouro (do dono do repo)

- **WhatsApp CTA destino fixo:** `+55 13 99103-9442` (não trocar)
- **Sequência mobile preservada:** Bluemed → hero → tiles 3x3 → resto
- **Paleta:** dourado (`#C9A24B → #A37714`) + azuis (`#152361`, `#0099D8`)
- **Não adicionar build system / framework** — é estático e tem que continuar sendo
- **Footer não tem texto financeiro** (já foi removido por decisão do dono)

## Estrutura de arquivos

```
bluemed-lp/
├── index.html                  ← TUDO está aqui (HTML+CSS+JS)
├── README.md                   ← visão geral
├── CLAUDE.md                   ← este arquivo
├── assets/                     ← imagens (logo, fotos, ícones)
├── specs/                      ← specs de design (referência)
├── uploads/                    ← assets de trabalho
└── copy-lp-bluemed-remodelada.md  ← doc da copy (referência)
```

## Contato

Dono do repo: Arthur Firmino (GitHub: @Firmino-Arthur)
WhatsApp do cliente (Paulo Flor): (13) 99720-1460
