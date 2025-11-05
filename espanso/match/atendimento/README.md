# Frases de Atendimento (Portuguese Customer Service Phrases)

This directory contains Portuguese snippets for customer service/medical attendance, converted from the `frases_atendimento.sql` dataset.

## Categories

### Início (inicio.yml)
Opening phrases and greetings for customer service interactions.

**Available triggers:**
- `:cumprimento-manha` - Morning greeting
- `:cumprimento-tarde` - Afternoon greeting  
- `:cumprimento-noite` - Evening greeting
- `:aguardar-momento` - Ask to wait a moment
- `:qual-duvida` - Ask about their question
- `:como-ajudar` - Ask how you can help
- `:pode-prosseguir` - Tell them to proceed

### Finalização (finalizacao.yml)
Closing phrases for ending customer service interactions.

**Available triggers:**
- `:troca-plantao` - Shift change notification
- `:despedida` - Farewell message
- `:encerrar-inatividade` - Close due to inactivity

## Usage Examples

Type any trigger followed by a space or enter to expand the phrase:

```
:cumprimento-manha → Bom dia, tudo bem? Qual seria a solicitação, por gentileza?
:despedida → Disponha! Bom plantão!
:aguardar-momento → Olá, tudo bem? Um momento, por favor. Estou finalizando a discussão de outro caso.
```

## Adding More Phrases

To add more customer service phrases:

1. Edit the appropriate YAML file (`inicio.yml` or `finalizacao.yml`)
2. Add new entries following this format:

```yaml
matches:
  - trigger: :your-trigger
    replace: Your phrase text here
```

3. Restart Espanso to load the new snippets:
   ```bash
   espanso restart
   ```

4. The changes will automatically sync to all your devices via Syncthing

## Source

These snippets were converted from the `frases_atendimento.sql` database provided by @alebmorais, containing standardized phrases for medical/customer service attendance in Portuguese.
