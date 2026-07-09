# Prospector de Sites

Prospecção semi-automática de clientes com sites ruins: acha, redesenha, publica e oferta.

## O ciclo

1. `/setup` — roda uma vez: assinatura, nichos padrão, conexão com GitHub e Vercel (com teste de publicação).
2. `/prospectar [nicho] [cidade]` — busca no Google Maps negócios nota ≥ 4.7 com site fraco e gera a planilha de leads no Google Sheets + `leads.md` com e-mail, motivo e ranking (padrão: 10 leads).
3. `/redesenhar` — recria as páginas dos 5+ melhores leads com estética premium, mantendo o conteúdo, logo e paleta reais do cliente. Gera junto o editor visual de cada página e o `comparar.html` (antes/depois lado a lado).
4. `/editor [cliente]` — (re)gera a versão editável no navegador (textos e imagens) com exportação da página final.
5. `/publicar [cliente|todos]` — cria um repositório GitHub por cliente (`[prefixo]-[slug]`) e um projeto Vercel vinculado a ele, publicando em `https://[prefixo]-[slug].vercel.app`. Novos pushes (ex.: depois de editar) republicam automaticamente.
6. `/proposta [cliente|todos]` — escreve o e-mail (rapport, sem preço) e cria o rascunho no Gmail.

## Requisitos

- Extensão Claude in Chrome conectada (prospecção no Maps e fallback de deploy)
- Conector do Gmail (rascunhos de proposta)
- Conector do Google Drive (planilha de leads)
- Pasta conectada no Cowork (armazena config, leads e sites)
- Conta no GitHub e conta na Vercel, com a integração Vercel-GitHub instalada

## Onde ficam os dados

Tudo na pasta conectada: `prospector-config.json` (preferências e credenciais — os tokens do GitHub e da Vercel ficam em texto no seu computador, nunca no chat), `leads.md` (pipeline), `sites/[slug]/` (página + editor de cada cliente) e `comparar.html` (comparador antes/depois). No GitHub, cada cliente vira um repositório próprio (`[prefixo]-[slug]`), publicado como projeto individual na Vercel.
