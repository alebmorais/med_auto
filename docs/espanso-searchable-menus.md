# Espanso Searchable Menus - Quick Reference

Learn how to create dropdown choice menus in Espanso that appear when you type a trigger.

## Basic Searchable Menu

Type `:dia` → Get a searchable menu of options:

```yaml
matches:
  - trigger: ":dia"
    replace: "{{greeting}}"
    vars:
      - name: greeting
        type: choice
        params:
          values:
            - "Bom dia! Como posso ajudar?"
            - "Boa tarde! Em que posso ser útil?"
            - "Boa noite! Como vai?"
```

**Usage:**
1. Type `:dia` + Space
2. Menu appears with 3 options
3. Use ↑↓ arrows or type to filter
4. Press Enter to select

## Menu with Labels (Descriptions)

Show friendly descriptions in the menu, insert short codes:

```yaml
matches:
  - trigger: ":med"
    replace: "{{term}}"
    vars:
      - name: term
        type: choice
        params:
          values:
            - label: "BP - Blood Pressure"
              value: "BP"
            - label: "HR - Heart Rate"
              value: "HR"
            - label: "Dx - Diagnosis"
              value: "Dx"
```

**Usage:**
- Type `:med`
- See "BP - Blood Pressure" in menu
- Select it → inserts "BP"

## Real-World Examples

### Customer Service (Portuguese)

```yaml
matches:
  - trigger: ":atend"
    replace: "{{response}}"
    vars:
      - name: response
        type: choice
        params:
          values:
            - label: "Saudação Manhã"
              value: "Bom dia! Tudo bem? Em que posso ajudar?"
            - label: "Saudação Tarde"
              value: "Boa tarde! Como vai? Qual seria a solicitação?"
            - label: "Agradecimento"
              value: "Muito obrigado pelo contato! Fico à disposição."
            - label: "Despedida"
              value: "Disponha! Tenha um ótimo dia!"
```

### Email Templates

```yaml
matches:
  - trigger: ":emailt"
    replace: "{{template}}"
    vars:
      - name: template
        type: choice
        params:
          values:
            - label: "Follow-up"
              value: |
                Hi,
                
                Just following up on my previous email.
                Looking forward to your response.
                
                Best regards
            - label: "Thank You"
              value: |
                Hi,
                
                Thank you for your help!
                I really appreciate it.
                
                Best
```

### Code Snippets

```yaml
matches:
  - trigger: ":code"
    replace: "{{snippet}}"
    vars:
      - name: snippet
        type: choice
        params:
          values:
            - label: "Python Function"
              value: |
                def function_name():
                    pass
            - label: "JavaScript Function"
              value: |
                function functionName() {
                  // code
                }
            - label: "For Loop (Python)"
              value: "for item in items:"
```

### Medical Examples

```yaml
matches:
  # Quick vitals
  - trigger: ":vital"
    replace: "{{vital}}"
    vars:
      - name: vital
        type: choice
        params:
          values:
            - "BP: 120/80 mmHg"
            - "HR: 72 bpm"
            - "Temp: 36.5°C"
            - "RR: 16/min"
            - "SpO2: 98%"
  
  # Common diagnoses
  - trigger: ":diag"
    replace: "{{diagnosis}}"
    vars:
      - name: diagnosis
        type: choice
        params:
          values:
            - "Hypertension"
            - "Diabetes Mellitus Type 2"
            - "Upper Respiratory Infection"
            - "Gastroenteritis"
```

## Advanced: Combining with Forms

Menu + user input prompts:

```yaml
matches:
  - trigger: ":bug"
    replace: |
      **Bug Report: {{type}}**
      
      Title: {{title}}
      
      {{description}}
      
      Severity: {{severity}}
    vars:
      - name: type
        type: choice
        params:
          values:
            - "UI Bug"
            - "Backend Bug"
            - "Performance Issue"
      - name: title
        type: form
        params:
          layout: "Bug Title: {{value}}"
      - name: description
        type: form
        params:
          layout: "Description: {{value}}"
          multiline: true
      - name: severity
        type: choice
        params:
          values:
            - "Critical"
            - "High"
            - "Medium"
            - "Low"
```

**Usage:**
1. Type `:bug`
2. Choose bug type from menu
3. Enter title in form
4. Enter description (multiline)
5. Choose severity from second menu
6. Full report is inserted

## Best Practices

### ✅ Do

- **Keep menus focused**: 5-15 items per menu
- **Use clear labels**: "BP - Blood Pressure" not just "BP"
- **Organize by category**: `:vital` for vitals, `:diag` for diagnoses
- **Put common items first**: Most-used options at the top
- **Make triggers memorable**: `:atend` for atendimento, `:med` for medical

### ❌ Don't

- **Avoid huge menus**: Split 30+ items into multiple menus
- **Don't use similar triggers**: `:med1`, `:med2` → use `:medterm`, `:medproc`
- **Don't mix languages**: Keep Portuguese in one menu, English in another
- **Avoid vague labels**: "Item 1", "Option A" → be descriptive

## File Organization

Create separate files for each category:

```
espanso/match/
├── atendimento/
│   ├── saudacoes.yml     # :atend - greetings menu
│   ├── respostas.yml     # :resp - responses menu
│   └── despedidas.yml    # :desp - farewells menu
├── medical/
│   ├── vitals.yml        # :vital - vitals menu
│   ├── diagnoses.yml     # :diag - diagnoses menu
│   └── procedures.yml    # :proc - procedures menu
├── code/
│   ├── python.yml        # :py - Python snippets menu
│   └── javascript.yml    # :js - JavaScript snippets menu
```

## Testing Your Menus

1. **Create the file** in `espanso/match/your-category/menu.yml`
2. **Restart Espanso**: `espanso restart`
3. **Test in a text editor**:
   - Type your trigger (e.g., `:dia`)
   - Press Space
   - Menu should appear
   - Use arrows or type to filter
   - Press Enter to select
4. **Debug if needed**:
   ```powershell
   # Windows
   espanso log
   
   # macOS/Linux
   espanso log
   ```

## Common Issues

### Menu doesn't appear
- Check YAML syntax (indentation must be exact, use spaces not tabs)
- Restart Espanso after changes
- Check logs: `espanso log`

### Wrong text inserted
- Check `value` field matches what you want inserted
- Verify no extra spaces or newlines

### Can't find items when typing
- Make sure labels/values contain the keywords you're searching for
- Labels are searchable, values are not (unless no label is provided)

## Quick Template

Copy this to start your own menu:

```yaml
matches:
  - trigger: ":your_trigger"
    replace: "{{selection}}"
    vars:
      - name: selection
        type: choice
        params:
          values:
            - label: "Option 1 Description"
              value: "Text to insert for option 1"
            - label: "Option 2 Description"
              value: "Text to insert for option 2"
            - label: "Option 3 Description"
              value: "Text to insert for option 3"
```

## Next Steps

1. Create your first menu using the template above
2. Save it in `espanso/match/personal/my-first-menu.yml`
3. Run `espanso restart`
4. Test it in a text editor
5. Add more options as you discover common phrases you type

For more advanced features, see:
- [Full Customization Guide](customization-guide.md)
- [Espanso Official Docs](https://espanso.org/docs/)
