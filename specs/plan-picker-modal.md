# Spec: Plan Picker + Lead Capture Modal

> Salvo antes de implementar. Referência completa para qualquer rollback / iteração futura.

## Objetivo
Adicionar seção "Escolha seu plano" com 3 cards (Sênior / PME / Adesão) + modal de captura de lead que redireciona para WhatsApp com mensagem pré-preenchida.

## Posicionamento decidido
**Substituir** o `.form-bar` atual (formulário inline curto) pela nova seção. Manter duas capturas competindo confunde o usuário.

## Cards (3, em grid horizontal, viram coluna no mobile)

| Card | Tag/Eyebrow | Subtitle | Ícone |
|------|-------------|----------|-------|
| **Plano Sênior** | "44+ anos" | "Individual ou Adesão" | coração / idoso |
| **PME** | "CNPJ ou MEI" | "A partir de 2 vidas" | prédio / empresa |
| **Adesão** | "Entidades de classe" | "OAB, CREA, sindicatos…" | grupo / coletivo |

Cada card tem botão "Quero esse plano →" no rodapé.

### Estados
- **Hover:** lift 6px + sombra ampliada + border ciano (1px var(--accent))
- **Ativo (clicado):** fundo gradient navy escuro + texto branco — espelha o card central da seção "Especialidades dos Centros Médicos" do bluemedsaude.com.br

## Modal (single-pane, centralizado)

Estrutura interna (top → bottom):
1. `[×]` no canto superior direito
2. Chip pílula amarelo com plano escolhido — ex: "PLANO SÊNIOR"
3. H3: "Receba sua cotação personalizada"
4. Subhead: "Preencha seus dados e um especialista entra em contato pelo WhatsApp em minutos."
5. Campos do formulário (dinâmicos por plano — ver abaixo)
6. Radio: "Tem plano de saúde há +6 meses?" (Sim / Não)
7. Checkbox LGPD obrigatório: "Aceito receber contato via WhatsApp (obrigatório por LGPD)"
8. CTA amarela com halo: "QUERO MINHA COTAÇÃO →"
9. Footer: 🔒 "Dados protegidos pela LGPD"

### Comportamento do modal
- **Abertura:** fadeIn + scaleIn (0.95 → 1)
- **Backdrop:** rgba(0,0,0,0.6) + backdrop-filter: blur(4px)
- **Fechamento:** [×], ESC, ou clique no backdrop
- **Mobile:** vira fullscreen com botão "voltar" no topo

## Campos por plano

### Sênior
- Nome completo (text, required)
- WhatsApp (tel, required, mask `(13) 99999-9999`)
- Idade (select: 44-48 / 49-53 / 54-58 / 59-63 / 64+)
- Cidade (select com cidades atendidas — usar as 9 da Baixada Santista por ora: Santos, Guarujá, São Vicente, Praia Grande, Cubatão, Bertioga, Mongaguá, Itanhaém, Peruíbe)
- Tem plano de saúde há +6 meses? (radio Sim/Não, required)

### PME
- Nome completo
- WhatsApp
- Quantas vidas? (select: 2-5 / 6-29 / 30-99 / 100+)
- Empresa tem plano há +6 meses? (radio Sim/Não)
- Nome da empresa (text, opcional)

### Adesão
- Nome completo
- WhatsApp
- Idade (mesmas faixas do Sênior)
- Qual entidade você é vinculado? (text, placeholder "ex: OAB-SP, CREA, sindicato…")
- Tem plano de saúde há +6 meses? (radio Sim/Não)

## Submit

Ao clicar em "QUERO MINHA COTAÇÃO":

1. Validar todos os required + LGPD marcado
2. POST `/api/lead` com payload JSON (no wireframe estático: apenas `console.log(payload)` — backend é integração futura)
3. Em paralelo, abrir `https://wa.me/55139XXXXXXXX?text={mensagem}` em nova aba
4. Mostrar tela de "Obrigado! Você está sendo redirecionado pro WhatsApp…" por 2-3s
5. Fechar modal

### Templates de mensagem WhatsApp

**Sênior:**
```
Olá! Vim pelo site da Blue Med e tenho interesse no PLANO SÊNIOR.

📋 Meus dados:
• Nome: {{nome}}
• Idade: {{idade}}
• Cidade: {{cidade}}
• Tenho plano há +6 meses: {{sim_nao}}

Pode me enviar uma cotação personalizada?
```

**PME:**
```
Olá! Vim pelo site da Blue Med e tenho interesse no PLANO PME.

🏢 Meus dados:
• Nome: {{nome}}
• Empresa: {{empresa}}
• Quantas vidas: {{vidas}}
• Empresa tem plano há +6 meses: {{sim_nao}}

Pode me enviar uma cotação?
```

**Adesão:**
```
Olá! Vim pelo site da Blue Med e tenho interesse no PLANO POR ADESÃO.

👤 Meus dados:
• Nome: {{nome}}
• Idade: {{idade}}
• Entidade: {{entidade}}
• Tenho plano há +6 meses: {{sim_nao}}

Pode me enviar uma cotação?
```

## Detalhes UX
- Form sem reload (transição suave entre estados)
- Erro de validação: borda do campo vermelha + mensagem abaixo
- Mantém os tokens CSS já definidos: `--primary: #152361`, `--accent: #0099D8`, `--yellow: #F5C518`, `--halo-yellow`, `--r-pill`, `--font: 'Montserrat'`

## Pendências de confirmação do usuário (antes de implementar)
1. **WhatsApp real**: número `55139XXXXXXXX` é placeholder. Usar `5513999999999` visível ou deixar `XXXXXXXX` para editar depois?
2. **Cidades Sênior**: spec falou "14 cidades atendidas" mas o site só lista 9 da Baixada Santista — quais as outras 5?
3. **POST /api/lead**: confirmar que no wireframe estático apenas simulamos com console.log.

## Backup
- Versão pré-implementação salva em: `Plano Senior Blue Med (v1 antes plan-picker).html`
