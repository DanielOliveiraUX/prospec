---
description: Escreve e envia (ou cria rascunho) da proposta por e-mail via Gmail
argument-hint: "[nome do cliente ou todos]"
---

Envie propostas para os leads com página publicada, seguindo a skill `proposta-email`.

## Passos

1. Leia `prospector-config.json` (assinatura e modo de envio) e `leads.md`.
2. Determine os destinatários: `$ARGUMENTS`, ou todos os leads com status `publicado` que ainda não receberam proposta. Somente leads com e-mail confirmado — para os demais, informe que a abordagem fica manual via WhatsApp (ofereça o texto adaptado).
3. Para cada cliente, escreva o e-mail seguindo a skill `proposta-email` na íntegra, usando os dados reais do lead: elogio baseado nas avaliações do Google, o defeito específico apontado na prospecção, o link da página antiga, o link da nova página publicada e a assinatura do config. NUNCA mencione preço.
4. Tire um screenshot da nova página (dobra inicial) e salve em `sites/[slug]/preview.png` — oriente o usuário a anexá-lo ao e-mail para aumentar a taxa de abertura.
5. Envio conforme o modo do config:
   - **rascunho** (padrão): crie o rascunho pelo conector do Gmail e informe que está pronto para revisão na caixa de rascunhos.
   - **enviar direto**: se o conector do Gmail não oferecer envio direto, use o Claude in Chrome no Gmail web para enviar, ou crie o rascunho e avise o usuário.
6. Atualize `leads.md`: status `proposta enviada` + data.

## Saída

Resuma: quantas propostas criadas/enviadas e para quem. Lembre o usuário de acompanhar as respostas — o fechamento (preço, reunião) acontece na resposta do cliente. Sugira um follow-up educado após 3-4 dias úteis sem resposta.
