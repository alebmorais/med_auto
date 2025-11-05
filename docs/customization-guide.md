# Customizing Your Snippets

This guide explains how to customize and create your own snippets for the Espanso system.

## Table of Contents
- [Understanding Snippet Structure](#understanding-snippet-structure)
- [Creating Simple Snippets](#creating-simple-snippets)
- [Multi-line Snippets](#multi-line-snippets)
- [Dynamic Content](#dynamic-content)
- [Variables and Forms](#variables-and-forms)
- [Organizing Snippets](#organizing-snippets)
- [Best Practices](#best-practices)

## Understanding Snippet Structure

Espanso snippets are defined in YAML files with the following basic structure:

```yaml
matches:
  - trigger: ":keyword"
    replace: "replacement text"
```

### Components:
- **matches**: Container for all snippets in the file
- **trigger**: The text you type to activate the snippet
- **replace**: The text that replaces the trigger

## Creating Simple Snippets

### Example 1: Simple Text Replacement

```yaml
matches:
  - trigger: ":addr"
    replace: "123 Main Street, City, State 12345"
  
  - trigger: ":email"
    replace: "your.email@example.com"
  
  - trigger: ":phone"
    replace: "+1 (555) 123-4567"
```

### Example 2: Abbreviations

```yaml
matches:
  - trigger: ":btw"
    replace: "by the way"
  
  - trigger: ":fyi"
    replace: "for your information"
  
  - trigger: ":asap"
    replace: "as soon as possible"
```

## Multi-line Snippets

Use the pipe `|` character for multi-line text:

```yaml
matches:
  - trigger: ":signature"
    replace: |
      Best regards,
      
      John Doe
      Senior Developer
      john.doe@company.com
      +1 (555) 123-4567
```

### Template Example

```yaml
matches:
  - trigger: ":meeting"
    replace: |
      MEETING NOTES
      Date: 
      Attendees: 
      
      Agenda:
      1. 
      
      Discussion Points:
      - 
      
      Action Items:
      - [ ] 
      
      Next Steps:
      - 
```

## Dynamic Content

### Dates and Times

Espanso can insert current date/time:

```yaml
matches:
  # Current date
  - trigger: ":date"
    replace: "{{mydate}}"
    vars:
      - name: mydate
        type: date
        params:
          format: "%Y-%m-%d"  # 2025-11-05
  
  # Current time
  - trigger: ":time"
    replace: "{{mytime}}"
    vars:
      - name: mytime
        type: date
        params:
          format: "%H:%M:%S"  # 14:30:45
  
  # Custom format
  - trigger: ":now"
    replace: "{{datetime}}"
    vars:
      - name: datetime
        type: date
        params:
          format: "%B %d, %Y at %I:%M %p"  # November 05, 2025 at 02:30 PM
```

### Date Format Codes

Common format codes:
- `%Y` - Year (4 digits): 2025
- `%y` - Year (2 digits): 25
- `%m` - Month (number): 11
- `%B` - Month (full name): November
- `%b` - Month (short): Nov
- `%d` - Day: 05
- `%H` - Hour (24h): 14
- `%I` - Hour (12h): 02
- `%M` - Minute: 30
- `%S` - Second: 45
- `%p` - AM/PM

## Variables and Forms

### Clipboard Variables

Use clipboard content in snippets:

```yaml
matches:
  - trigger: ":link"
    replace: "[{{clipboard}}]({{clipboard}})"
    vars:
      - name: clipboard
        type: clipboard
  
  - trigger: ":quote"
    replace: |
      > {{text}}
      
      — Author
    vars:
      - name: text
        type: clipboard
```

### Form Variables (User Input)

Prompt for user input:

```yaml
matches:
  - trigger: ":greet"
    replace: "Hello {{name}}, how are you today?"
    vars:
      - name: name
        type: form
        params:
          layout: "Name: {{value}}"
  
  - trigger: ":bug"
    replace: |
      BUG REPORT
      
      Title: {{title}}
      Severity: {{severity}}
      
      Description:
      {{description}}
      
      Steps to Reproduce:
      {{steps}}
      
      Expected Result:
      {{expected}}
      
      Actual Result:
      {{actual}}
    vars:
      - name: title
        type: form
        params:
          layout: "Bug Title: {{value}}"
      - name: severity
        type: choice
        params:
          values:
            - Critical
            - High
            - Medium
            - Low
      - name: description
        type: form
        params:
          layout: "Description: {{value}}"
          multiline: true
      - name: steps
        type: form
        params:
          layout: "Steps: {{value}}"
          multiline: true
      - name: expected
        type: form
        params:
          layout: "Expected: {{value}}"
      - name: actual
        type: form
        params:
          layout: "Actual: {{value}}"
```

### Shell Commands

Execute shell commands:

```yaml
matches:
  - trigger: ":git"
    replace: "{{output}}"
    vars:
      - name: output
        type: shell
        params:
          cmd: "git branch --show-current"
  
  - trigger: ":ip"
    replace: "{{myip}}"
    vars:
      - name: myip
        type: shell
        params:
          cmd: "curl -s https://api.ipify.org"
```

### Random Choices

Select from predefined options:

```yaml
matches:
  - trigger: ":coin"
    replace: "{{result}}"
    vars:
      - name: result
        type: random
        params:
          choices:
            - "Heads"
            - "Tails"
  
  - trigger: ":greeting"
    replace: "{{greet}}"
    vars:
      - name: greet
        type: random
        params:
          choices:
            - "Hello!"
            - "Hi there!"
            - "Hey!"
            - "Greetings!"
```

## Organizing Snippets

### Directory Structure

Organize snippets by category in subdirectories:

```
espanso/match/
├── work/
│   ├── emails.yml
│   ├── signatures.yml
│   └── reports.yml
├── personal/
│   ├── common.yml
│   └── social.yml
├── code/
│   ├── python.yml
│   ├── javascript.yml
│   └── git.yml
└── medical/
    ├── common.yml
    └── procedures.yml
```

### File Naming

Use descriptive names:
- `common.yml` - General snippets for the category
- `templates.yml` - Template snippets
- `abbreviations.yml` - Short abbreviations
- `specific-topic.yml` - Topic-specific snippets

### Categories by Domain

Examples:

**Work Category**:
```yaml
# work/emails.yml
matches:
  - trigger: ":reply"
    replace: "Thank you for your email. I will review and get back to you shortly."
  
  - trigger: ":ooo"
    replace: |
      Out of Office
      
      I am currently out of the office and will return on {{date}}.
      For urgent matters, please contact {{contact}}.
    vars:
      - name: date
        type: form
      - name: contact
        type: form
```

**Code Category**:
```yaml
# code/python.yml
matches:
  - trigger: ":pyclass"
    replace: |
      class {{classname}}:
          def __init__(self):
              pass
    vars:
      - name: classname
        type: form
  
  - trigger: ":pyfor"
    replace: "for {{item}} in {{list}}:"
    vars:
      - name: item
        type: form
      - name: list
        type: form
```

## Best Practices

### Trigger Naming

1. **Use prefixes**: Start triggers with `:` or `;` to avoid accidental expansion
   ```yaml
   trigger: ":email"  # Good
   trigger: "email"   # Bad - might trigger unintentionally
   ```

2. **Be consistent**: Use the same naming convention throughout
   ```yaml
   :myaddr  # Abbreviation style
   :my-email  # Kebab-case style
   :myPhone  # camelCase style
   ```

3. **Make it memorable**: Use intuitive abbreviations
   ```yaml
   :addr → address  # Clear
   :ea → email address  # Not obvious
   ```

### Organization

1. **Group related snippets**: Keep similar snippets in the same file
2. **Use comments**: Document your snippets
   ```yaml
   # Customer service responses
   matches:
     - trigger: ":thanks"
       replace: "Thank you for contacting us!"
   ```

3. **Separate concerns**: Different files for different contexts
   - Personal vs. work
   - Different programming languages
   - Different projects

### Performance

1. **Avoid too many snippets**: Espanso performance can degrade with thousands of snippets
2. **Use specific triggers**: More specific triggers = faster matching
3. **Disable unused categories**: Comment out or remove snippets you don't use

### Testing

1. **Test in a text editor** before using in production
2. **Verify multi-line formatting** looks correct
3. **Test variables** to ensure they work as expected
4. **Check for conflicts**: Ensure triggers don't overlap

### Syncing Considerations

1. **Test on all platforms**: Some features may work differently on Windows vs. macOS
2. **Use cross-platform paths**: Avoid OS-specific paths in snippets
3. **Restart Espanso after sync**: Changes require restart to take effect

## Advanced Examples

### Conditional Logic (Using Scripts)

```yaml
matches:
  - trigger: ":greettime"
    replace: "{{greeting}}"
    vars:
      - name: greeting
        type: shell
        params:
          cmd: |
            hour=$(date +%H)
            if [ $hour -lt 12 ]; then
              echo "Good morning"
            elif [ $hour -lt 18 ]; then
              echo "Good afternoon"
            else
              echo "Good evening"
            fi
```

### Complex Templates

```yaml
matches:
  - trigger: ":pr"
    replace: |
      ## Pull Request: {{title}}
      
      ### Description
      {{description}}
      
      ### Changes Made
      - {{changes}}
      
      ### Testing Done
      {{testing}}
      
      ### Screenshots (if applicable)
      {{screenshots}}
      
      ### Checklist
      - [ ] Code follows project style guidelines
      - [ ] Self-reviewed the code
      - [ ] Commented hard-to-understand areas
      - [ ] Updated documentation
      - [ ] No new warnings
      - [ ] Added tests
      - [ ] All tests pass
      - [ ] No merge conflicts
      
      Fixes #{{issue}}
    vars:
      - name: title
        type: form
      - name: description
        type: form
        params:
          multiline: true
      - name: changes
        type: form
        params:
          multiline: true
      - name: testing
        type: form
        params:
          multiline: true
      - name: screenshots
        type: form
      - name: issue
        type: form
```

## Debugging Tips

### Check YAML Syntax

Invalid YAML will prevent snippets from loading:

```bash
# On macOS/Linux with yamllint
yamllint espanso/match/your-file.yml

# Check Espanso logs for errors
espanso log
```

### Common Errors

1. **Indentation**: YAML requires consistent indentation (use spaces, not tabs)
2. **Special characters**: Quote strings with special characters
3. **Duplicate triggers**: Each trigger must be unique
4. **Missing vars**: All variables in `replace` must be defined in `vars`

## Resources

- [Espanso Official Documentation](https://espanso.org/docs/)
- [YAML Syntax Guide](https://yaml.org/spec/1.2/spec.html)
- [Date Format Reference](https://strftime.org/)
- [Espanso Packages Hub](https://hub.espanso.org/)

## Next Steps

1. Create your first custom snippet
2. Test it with Espanso
3. Organize snippets by category
4. Share with other devices via Syncthing
5. Iterate and improve based on usage