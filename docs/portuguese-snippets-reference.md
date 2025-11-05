# Quick Reference - Portuguese Customer Service Snippets

This document provides a quick reference for all Portuguese customer service snippets from the `frases_atendimento.sql` dataset.

## Início (Opening Phrases)

| Trigger | Portuguese Text | English Translation |
|---------|----------------|---------------------|
| `:cumprimento-manha` | Bom dia, tudo bem? Qual seria a solicitação, por gentileza? | Good morning, how are you? What would be the request, please? |
| `:cumprimento-tarde` | Boa tarde, tudo bem? Qual seria a solicitação, por gentileza? | Good afternoon, how are you? What would be the request, please? |
| `:cumprimento-noite` | Boa noite, tudo bem? Qual seria a solicitação, por gentileza? | Good evening, how are you? What would be the request, please? |
| `:aguardar-momento` | Olá, tudo bem? Um momento, por favor. Estou finalizando a discussão de outro caso. | Hello, how are you? One moment, please. I'm finishing the discussion of another case. |
| `:qual-duvida` | Qual seria a dúvida, por gentileza? | What would be the question, please? |
| `:como-ajudar` | Em que posso ajudar, por gentileza? | How can I help you, please? |
| `:pode-prosseguir` | Pode prosseguir. | You may proceed. |

## Finalização (Closing Phrases)

| Trigger | Portuguese Text | English Translation |
|---------|----------------|---------------------|
| `:troca-plantao` | Desculpe, mas está havendo troca de horário e outro colega assumirá o caso e sua discussão terá continuidade, ok? Obrigada | Sorry, but there's a shift change happening and another colleague will take over the case and your discussion will continue, ok? Thank you |
| `:despedida` | Disponha! Bom plantão! | You're welcome! Have a good shift! |
| `:encerrar-inatividade` | Devido a inatividade do chat, o atendimento será encerrado. Caso precise, entre em contato novamente. | Due to chat inactivity, the service will be closed. If needed, contact us again. |

## Usage

Simply type any trigger followed by a space or enter key to expand the full phrase. These snippets will sync across all your devices automatically via Syncthing.

## File Locations

- Opening phrases: `espanso/match/atendimento/inicio.yml`
- Closing phrases: `espanso/match/atendimento/finalizacao.yml`
- Documentation: `espanso/match/atendimento/README.md`

## Data Source

Converted from `frases_atendimento.sql` provided by @alebmorais.
