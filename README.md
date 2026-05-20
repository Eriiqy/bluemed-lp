# Blue Med — Landing Page Plano Sênior

Landing page estática do Plano Sênior Blue Med Saúde para a Baixada Santista.

> **Trabalhando neste repo com Claude?** Leia [`CLAUDE.md`](./CLAUDE.md) primeiro — tem o briefing completo (stack, deploy, pixels, domínio).

## Stack
HTML/CSS/JS puro, single-file (`index.html`). Sem build, sem dependências.

## Deploy
Static hosting na Vercel. Cada push em `main` faz redeploy automático.

## Estrutura
- `index.html` — landing page completa (hero, planos, rede, vídeo, FAQ, modal de cotação)
- `assets/` — imagens (logo, fotos hospital/laboratório, badges, etc.)
- `specs/` — especificações de design
- `uploads/` — assets de trabalho/staging
- `copy-lp-bluemed-remodelada.md` — doc da copy aplicada (Schwartz + PQV)

## Rodar localmente
```bash
python3 -m http.server 8765
# abrir http://localhost:8765
```

## Contato
WhatsApp Paulo Flor: (13) 99720-1460
