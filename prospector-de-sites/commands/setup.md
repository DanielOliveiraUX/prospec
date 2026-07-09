---
description: Configura o plugin — assinatura, preferências e conexão com GitHub e Vercel (roda uma vez)
---

Configure o ambiente do Prospector de Sites. Siga esta ordem:

## 1. Pasta de trabalho

Verifique se há uma pasta do usuário conectada. Se não houver, peça para conectar uma pasta (ex.: "Clientes") usando a ferramenta de solicitação de pasta — tudo (config, leads e sites criados) será salvo nela para persistir entre sessões.

## 2. Verificar config existente

Procure `prospector-config.json` na pasta conectada. Se existir, mostre um resumo (sem exibir a senha) e pergunte o que o usuário quer atualizar. Se não existir, colete os dados abaixo.

## 3. Dados do usuário (perguntar via AskUserQuestion / formulário)

Colete:

- **Assinatura da proposta**: nome completo, como quer se apresentar (ex.: "Designer de páginas de alta conversão") e WhatsApp/telefone de contato.
- **Nichos padrão de prospecção**: sugira nutricionistas, psicólogos, advogados e psiquiatras como ponto de partida, mas deixe o usuário editar livremente.
- **Cidade/região padrão**.
- **Leads qualificados por busca**: padrão 10.
- **Modo de envio da proposta**: padrão "criar rascunho no Gmail para revisão" (recomendado). Alternativa: enviar direto.

## 4. Conexão com GitHub + Vercel

Pergunte se o usuário já tem conta no GitHub e na Vercel.

- **Se ainda não tem**: explique brevemente que ambas são gratuitas — criar conta em github.com e depois em vercel.com usando o botão "Continue with GitHub" (isso já vincula as duas contas). Depois de criar, ele deve voltar e rodar `/setup` de novo. Salve o config parcial e encerre.
- **Se já tem**: colete:
  1. **Usuário ou organização do GitHub** que será dona dos repositórios dos clientes.
  2. **Visibilidade dos repositórios**: padrão `private` (recomendado — são sites de leads ainda não fechados), alternativa `public`.
  3. **Prefixo dos repositórios**: padrão `site` (cada cliente vira `[prefixo]-[slug]`, ex.: `site-jessica-nutri`).
  4. **Personal Access Token do GitHub** com escopo `repo` (gerado em github.com/settings/tokens). **NUNCA deve ser digitado no chat.**
  5. **Token de API da Vercel** (gerado em vercel.com/account/tokens). **NUNCA deve ser digitado no chat.**
  6. **Time da Vercel** (opcional — só se o token pertencer a uma conta de equipe/organização na Vercel).

  Para os dois tokens: salve o config com os campos vazios (`"token": ""`) e instrua o usuário a abrir `prospector-config.json` na pasta conectada e preenchê-los ele mesmo (avisando que ficam em texto no computador dele). Avise também que a **integração "Vercel" precisa estar instalada no GitHub** (uma vez só): se não estiver, oriente a abrir `https://github.com/apps/vercel` e autorizar o acesso aos repositórios/organização. Só depois disso rode o teste de conexão. Nunca exiba, imprima ou registre os tokens em nenhuma saída.

## 5. Salvar e testar

Salve tudo em `prospector-config.json` na pasta conectada, neste formato:

```json
{
  "assinatura": { "nome": "", "apresentacao": "", "whatsapp": "" },
  "prospeccao": { "nichos": ["nutricionistas", "psicologos", "advogados", "psiquiatras"], "cidade": "", "leadsPorBusca": 10 },
  "envio": { "modo": "rascunho" },
  "github": { "usuario": "", "org": "", "visibilidade": "private", "prefixoRepo": "site", "token": "" },
  "vercel": { "token": "", "team": "" }
}
```

Se os tokens do GitHub e da Vercel foram preenchidos, teste a conexão seguindo a skill `deploy-github-vercel`: crie o repositório `[prefixoRepo]-teste`, publique uma página simples, crie o projeto Vercel vinculado e informe a URL pública (`https://[prefixoRepo]-teste.vercel.app`) ao usuário. Se o teste falhar, diagnostique (token/escopo, integração Vercel-GitHub não instalada, nome de repositório já existente) antes de concluir.

## 6. Encerrar

Confirme o que foi salvo e explique o ciclo: `/prospectar` → `/redesenhar` → `/publicar` → `/proposta`, com `/editor` opcional para ajustes manuais. Lembre que, depois de publicado, qualquer atualização exportada pelo `/editor` só precisa de um novo `/publicar` — a Vercel republica sozinha a cada push.
