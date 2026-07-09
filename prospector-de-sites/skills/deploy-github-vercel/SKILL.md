---
name: deploy-github-vercel
description: Esta skill deve ser usada ao publicar páginas no GitHub (um repositório por cliente) com deploy automático na Vercel — criação do repositório, push do index.html, criação/link do projeto Vercel, verificação da URL pública e fallback pelo navegador. Acione quando o usuário disser "publicar", "subir o site", "colocar no ar", "deploy", "github", "vercel" ou rodar /publicar ou o teste de conexão do /setup.
---

# Deploy no GitHub + Vercel

Cada cliente vira **um repositório GitHub próprio** (`[prefixoRepo]-[slug]`, ex.: `site-jessica-nutri`) com o site publicado como `index.html` na raiz. Esse repositório fica conectado a **um projeto Vercel** que builda e publica automaticamente a cada push — depois da configuração inicial, nenhum passo manual de deploy é necessário.

Credenciais: ler de `prospector-config.json` (`github.token`, `github.usuario`/`github.org`, `github.visibilidade`, `github.prefixoRepo`, `vercel.token`, `vercel.team`). Se faltar algum token, NUNCA pedir no chat: instruir o usuário a preenchê-lo diretamente no `prospector-config.json`. Ao usar os tokens em comandos, leia-os do arquivo dentro do próprio comando — nunca exibi-los em saídas ou logs.

## Método 1 — API programática (tentar primeiro)

### 1. Criar o repositório

```bash
curl -sS -X POST \
  -H "Authorization: token $GITHUB_TOKEN" \
  -H "Accept: application/vnd.github+json" \
  "https://api.github.com/user/repos" \
  -d "{\"name\":\"REPO\",\"private\":true,\"auto_init\":false}"
```

Se `github.org` estiver preenchido, usar `https://api.github.com/orgs/ORG/repos` em vez de `/user/repos`. `private` segue `github.visibilidade` do config. Se o repositório já existir (ex.: reenvio após edição), pular esta etapa e ir direto para o push do conteúdo.

### 2. Enviar o index.html (Contents API — sem precisar de git local)

```bash
CONTENT_B64=$(base64 -w0 sites/SLUG/SLUG.html)
curl -sS -X PUT \
  -H "Authorization: token $GITHUB_TOKEN" \
  -H "Accept: application/vnd.github+json" \
  "https://api.github.com/repos/OWNER/REPO/contents/index.html" \
  -d "{\"message\":\"Publica site\",\"content\":\"$CONTENT_B64\"}"
```

Se o arquivo já existir (atualização após uma edição no `/editor`), a API exige o `sha` atual: buscar antes com `GET .../contents/index.html` e incluir `"sha":"..."` no corpo do PUT. Se houver imagens/assets locais além do HTML autocontido, enviar cada uma da mesma forma em `assets/nome-do-arquivo`.

### 3. Criar (ou confirmar) o projeto Vercel vinculado ao repositório

```bash
curl -sS -X POST \
  -H "Authorization: Bearer $VERCEL_TOKEN" \
  "https://api.vercel.com/v10/projects" \
  -d "{\"name\":\"REPO\",\"gitRepository\":{\"type\":\"github\",\"repo\":\"OWNER/REPO\"}}"
```

Adicionar `?teamId=TEAM` na URL se `vercel.team` estiver preenchido no config. Isso já dispara o primeiro deploy automaticamente (a Vercel lê o commit que acabou de ser enviado no passo 2). Se o projeto já existir (reenvio após edição), este passo é opcional — o push do passo 2 sozinho já dispara um novo deploy.

Pré-requisito único: a integração/app "Vercel" precisa estar instalada no GitHub do usuário (feito uma vez no `/setup`), senão a criação do projeto vinculado falha com erro de permissão.

### 4. Acompanhar o deploy

```bash
curl -sS -H "Authorization: Bearer $VERCEL_TOKEN" \
  "https://api.vercel.com/v6/deployments?projectId=PROJECT_ID&limit=1"
```

Aguardar `readyState` = `READY` (normalmente 10-30s). A URL pública fica em `https://REPO.vercel.app` (campo `url`/`alias` da resposta).

## Método 2 — fallback pelo navegador (se a API falhar ou faltar token)

Usar o Claude in Chrome:

1. `https://github.com/new` — nome `REPO`, visibilidade conforme config, criar.
2. Na tela do repositório vazio, "uploading an existing file" → arrastar `sites/SLUG/SLUG.html` renomeado para `index.html` → commit.
3. `https://vercel.com/new` — "Import Git Repository" → selecionar `OWNER/REPO` (se não aparecer na lista, a integração Vercel-GitHub não está instalada: abrir `https://github.com/apps/vercel` e autorizar o acesso ao repositório/organização primeiro) → Deploy.
4. Copiar a URL de produção que a Vercel mostra ao final do deploy.

## Verificação (sempre)

Abrir a URL pública e confirmar HTTP 200 + conteúdo correto (título do cliente presente). Se der erro, checar: token com escopo certo (`repo` no GitHub), integração Vercel-GitHub instalada, nome de repositório/projeto duplicado (a Vercel exige nomes de projeto únicos por conta — se colidir, sufixar com `-2`).

## Organização

- 1 repositório = 1 projeto Vercel = 1 cliente: `[prefixoRepo]-[slug]`, slug em kebab-case sem acentos (ex.: `site-jessica-nutri`).
- Nunca reutilizar/sobrescrever o repositório de outro cliente.
- Atualizações depois da publicação (ex.: saída do `/editor`) são só um novo push no mesmo repositório — a Vercel republica sozinha, sem precisar recriar o projeto.
- Repositório de teste do `/setup`: `[prefixoRepo]-teste` com um "Funcionou!" simples.
- Se o cliente fechar negócio e quiser o domínio próprio, basta adicionar o domínio dele no painel do projeto na Vercel (Settings → Domains) — não muda nada no fluxo do plugin.
