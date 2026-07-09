---
description: Publica as páginas redesenhadas no GitHub (1 repositório por cliente) com deploy automático na Vercel, e retorna as URLs públicas
argument-hint: "[nome do cliente ou todos]"
---

Publique páginas no GitHub + Vercel seguindo a skill `deploy-github-vercel`.

## Passos

1. Leia `prospector-config.json`. Se `github.token` ou `vercel.token` não estiverem preenchidos, oriente o usuário a preenchê-los diretamente no config (nunca peça os tokens no chat) — não prossiga sem eles.
2. Determine o que publicar: `$ARGUMENTS` (um cliente ou "todos"), ou liste as páginas com status `redesenhado` em `leads.md` e pergunte.
3. Para cada página, siga a skill `deploy-github-vercel`: crie o repositório `[prefixoRepo]-[slug]` no GitHub (visibilidade do config) sob `github.org` ou `github.usuario`, envie `sites/[slug]/[slug].html` como `index.html` na raiz (se o repositório já existir por causa de uma reedição, apenas atualize o arquivo), crie/confirme o projeto Vercel vinculado ao repositório e aguarde o primeiro deploy concluir. Tentando primeiro o método programático (API do GitHub + API da Vercel) e usando o fallback pelo navegador (github.com/new + vercel.com/new) se necessário.
4. Verifique cada publicação abrindo a URL pública (`https://[prefixoRepo]-[slug].vercel.app`) e confirmando que a página carrega corretamente.
5. Atualize `leads.md`: status `publicado` + coluna com a URL pública.

## Saída

Liste as URLs públicas de cada cliente. Lembre que futuras edições exportadas pelo `/editor` só precisam de um novo `/publicar` — a Vercel republica sozinha a cada push. Sugira o próximo passo: `/proposta` para enviar os e-mails.
