# Testing GitHub Markdown Features

This document tests various markdown features supported by GitHub and most markdown parsers.

## Table of Contents
- [Headers](#headers)
- [Text Formatting](#text-formatting)
- [Lists](#lists)
- [Links and Images](#links-and-images)
- [Code](#code)
- [Tables](#tables)
- [Advanced Features](#advanced-features)

## Headers

# H1 - Main Title
## H2 - Section Title
### H3 - Subsection Title
#### H4 - Minor Heading
##### H5 - Small Heading
###### H6 - Smallest Heading

## Text Formatting

**Bold text using asterisks**
__Bold text using underscores__

*Italic text using asterisks*
_Italic text using underscores_

~~Strikethrough text~~

**You can _combine_ formatting**

## Lists

### Unordered Lists
- First item
- Second item
  - Nested item
  - Another nested item
    - Deep nested item
- Third item

### Ordered Lists
1. First ordered item
2. Second ordered item
   1. Nested ordered item
   2. Another nested item
3. Third ordered item

### Mixed Lists
1. Ordered item
   - Unordered nested item
   - Another unordered item
2. Another ordered item

## Links and Images

### Links
[GitHub](https://github.com)
[Link with title](https://github.com "GitHub Homepage")

### Automatic Links
https://www.github.com
user@example.com

### Images
![GitHub Logo](https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png)

## Code

### Inline Code
Use `git status` to check repository status.
Install with `npm install package-name`.

### Code Blocks

```bash
git clone https://github.com/user/repo.git
cd repo
npm install
npm start
```

```javascript
function greetUser(name) {
    if (!name) {
        return "Hello, Anonymous!";
    }
    return `Hello, ${name}!`;
}

console.log(greetUser("Developer"));
```

```python
def calculate_total(items):
    total = 0
    for item in items:
        total += item['price'] * item['quantity']
    return total

# Example usage
shopping_cart = [
    {'name': 'laptop', 'price': 999.99, 'quantity': 1},
    {'name': 'mouse', 'price': 25.50, 'quantity': 2}
]

print(f"Total: ${calculate_total(shopping_cart):.2f}")
```

```json
{
  "name": "test-project",
  "version": "1.0.0",
  "description": "A sample project for testing",
  "main": "index.js",
  "scripts": {
    "start": "node index.js",
    "test": "jest"
  },
  "dependencies": {
    "express": "^4.18.0",
    "lodash": "^4.17.21"
  }
}
```

## Tables

### Basic Table
| Feature | Support | Notes |
|---------|---------|-------|
| Headers | ✅ | All levels supported |
| Lists | ✅ | Ordered and unordered |
| Code | ✅ | Inline and blocks |
| Tables | ✅ | This one! |

### Aligned Columns
| Left Aligned | Center Aligned | Right Aligned |
|:-------------|:--------------:|--------------:|
| Content | Content | Content |
| More content | More content | More content |
| Even more | Even more | Even more |

## Advanced Features

### Blockquotes
> This is a simple blockquote.
> 
> It can span multiple lines.

> ### Blockquotes can contain headers
> 
> 1. And lists
> 2. Like this one
> 
> Even **formatting** works inside blockquotes.

### Nested Blockquotes
> First level quote
> > Second level quote
> > > Third level quote

### Horizontal Rules

---

***

___

### Task Lists
- [x] Completed task
- [x] Another completed task
- [ ] Incomplete task
- [ ] Another incomplete task
  - [x] Nested completed task
  - [ ] Nested incomplete task

### Mentions and References
@username - User mention
#123 - Issue reference
user/repo#123 - Cross-repository reference

### Emoji Support
:rocket: :heart: :+1: :-1: :smile: :tada:

### Line Breaks and Paragraphs

This is the first paragraph.

This is the second paragraph with a line break.  
This line has two spaces at the end for a manual line break.

### Escape Characters
\*This text is not italic\*
\`This is not code\`
\# This is not a header

### HTML in Markdown
<details>
<summary>Click to expand</summary>

This content is hidden by default and can be expanded by clicking the summary.

You can put **markdown** inside HTML tags too!

</details>

<kbd>Ctrl</kbd> + <kbd>C</kbd> to copy
<kbd>Ctrl</kbd> + <kbd>V</kbd> to paste

### Mathematical Expressions (if supported)
```
E = mc²
x = (-b ± √(b²-4ac)) / 2a
```

### Footnotes
This text has a footnote[^1].

[^1]: This is the footnote content.

---
