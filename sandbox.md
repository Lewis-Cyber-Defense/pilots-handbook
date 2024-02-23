---
title: Sandbox
description: Use this page to test formatting
published: false
date: 2024-02-23T22:45:42.745Z
tags: 
editor: markdown
dateCreated: 2024-02-22T19:52:30.584Z
---

Use this page for Markdown snippets

# Embedding Images

## Embedding Images {.tabset}

### Preview

![Image with 64x64 Pixel](/assets/site/baukasten.png =64x)

This image is directly ![Image with 16x16 Pixel](/assets/site/baukasten.png =16x) embedded into the text (inline).
For a good text flow the image has been resized to `16x16 Pixel`.
Without defining the optional setting for the image resolution the image would be in full resolution.

### Source

```markdown
![Image with 64x64 Pixel](/assets/site/baukasten.png =64x)

This image is directly ![Image with 16x16 Pixel](/assets/site/baukasten.png =16x) embedded into the text (inline).
For a good text flow the image has been resized to `16x16 Pixel`.
Without defining the optional setting for the image resolution the image would be in full resolution.
```

# Embedding Images (with Spoiler)

## Embedding Images (with Spoiler) {.tabset}

### Preview

<details>

![Image inside Spoiler](/assets/site/baukasten.png)

</details>

### Source

```markdown
<details>

![Image inside Spoiler](/assets/site/baukasten.png)

</details>
```

# Blockquote

## Blockquote {.tabset}

### Preview

> This is a regular blockquote.

> This is a `{.is-info}` blockquote.
{.is-info}

> This is a `{.is-success}` blockquote.
{.is-success}

> This is a `{.is-warning}` blockquote.
{.is-warning}

> This is a `{.is-danger}` blockquote.
{.is-danger}

### Source

```
> This is a regular blockquote.

> This is a `{.is-info}` blockquote.
{.is-info}

> This is a `{.is-success}` blockquote.
{.is-success}

> This is a `{.is-warning}` blockquote.
{.is-warning}

> This is a `{.is-danger}` blockquote.
{.is-danger}
```

# Emojis

## Emojis {.tabset}

### Preview

There are a variety of emojis available like :palm_tree: or :tea:.

### Source

```markdown
There are a variety of emojis available like :palm_tree: or :tea:.
```

### Remarks

> For a detailed overview of all supported Emojis you can check out this [cheatsheet](https://www.webfx.com/tools/emoji-cheat-sheet/).
>
> If the website is not reachable anymore you could also check out this local [image](/assets/site/emojis.png).
{.is-info}

# Footnotes

## Footnotes {.tabset}

### Preview

This text[^1] contains two footnotes[^2].
The footnotes can be found at the end of a page.

[^1]: This is the first footnote.
[^2]: This is the second footnote.

### Source

```markdown
This text[^1] contains two footnotes[^2].
The footnotes can be found at the end of a page.

[^1]: This is the first footnote.
[^2]: This is the second footnote.
```

# Horizontal Line

## Horizontal Line {.tabset}

### Preview

This text is separated by a

---

horizontal line.

### Source

```markdown
This text is separated by a

---

horizontal line.
```

# Hyperlinks

## Hyperlinks {.tabset}

### Preview

Hyperlinks can be defined as [text](/Markdown-Syntax) or embedded in an image [![Image with 16x16 Pixel](/assets/site/baukasten.png =16x)](/Markdown-Syntax).

### Source

```markdown
Hyperlinks can be defined as [text](/Markdown-Syntax) or embedded in an image [![Image with 16x16 Pixel](/assets/site/baukasten.png =16x)](/Markdown-Syntax).
```

# List (Tasks)

## List (Tasks) {.tabset}

### Preview

- [X] This list entry has been completed.
- [ ] This list entry is still pending.

### Source

```markdown
- [X] This list entry has been completed.
- [ ] This list entry is still pending.
```

# List (Numerical)

## List (Numerical) {.tabset}

### Preview

1. First Entry
2. Second Entry
3. Third Entry

### Source

```markdown
1. First Entry
1. Second Entry
1. Third Entry
```

### Remarks

> It's enough to set all list entries to `1.`.
> When rendering the page all the entries will be correctly numbered in the order they are written down in the source.
{.is-info}

# List (Non-Numerical)

## List (Non-Numerical) {.tabset}

### Preview

Here's an example of a simple list:
- A simple list element.
- Another simple list element.
- And another simple list element.

Here's an example of a grid list:
- A grid element.
- Another grid element.
- And another grid element.
{.grid-list}

Here's an example of a formatted hyperlink list:
- [A hyperlink *with description*](/Markdown-Syntax)
- [Another hyperlink *with additional description*](/Markdown-Syntax)
- [And another hyperlink *also with addition description*](/Markdown-Syntax)
{.links-list}

### Source

```markdown
Here's an example of a simple list:
- A simple list element.
- Another simple list element.
- And another simple list element.

Here's an example of a grid list:
- A grid element.
- Another grid element.
- And another grid element.
{.grid-list}

Here's an example of a formatted hyperlink list:
- [A hyperlink *with description*](/Markdown-Syntax)
- [Another hyperlink *with additional description*](/Markdown-Syntax)
- [And another hyperlink *also with addition description*](/Markdown-Syntax)
{.links-list}
```

# Sourcecode

## Sourcecode {.tabset}

### Preview

This text contains `Inline` source code formatting.

```
This whole textblock is formatted as sourcecode.
```

Example with syntax highlighting:

```csharp
public static void Main(string[] args)
{
		Environment.Exit(0);
}
```

### Source

````markdown
This text contains `Inline` source code formatting.

```
This whole textblock is formatted as sourcecode.
```

Example with syntax highlighting:

```csharp
public static void Main(string[] args)
{
		Environment.Exit(0);
}
```
````

# Spoiler

## Spoiler {.tabset}

### Preview

<details>
  <summary>Warning: Spoiler</summary>
  This is a spoiler text.
</details>

### Source

```markdown
<details>
  <summary>Warning: Spoiler</summary>
  This is a spoiler text.
</details>
```

# Table

## Table {.tabset}

### Preview

| Name            | Type    | Value |
|:--------------- |:-------:| -----:|
| First Element   | First   |  1600 |
| Second Element  | Second  |    12 |
| Third Element   | Third   |     1 |

### Source

```markdown
| Name            | Type    | Value |
|:--------------- |:-------:| -----:|
| First Element   | First   |  1600 |
| Second Element  | Second  |    12 |
| Third Element   | Third   |     1 |
```

# Tabs

## Tabs {.tabset}

### Preview

The Tabs, which are quite present on this particular page, should be good examples already.

### Source

````markdown
## Tabs {.tabset}

### Preview

The Tabs, which are quite present on this particular page, should be good examples already.

### Source

```markdown
TEXT
```
````

### Remarks

> Tabs can only be used if they are declared with a caption.
> Like it is being shown in the example the content of a tab needs to be between the caption and a `{.tabset}` element.
> For each tab you need to define an additional Caption.
{.is-info}

# Keyboard symbols

## Keyboard symbols {.tabset}

### Preview

Keyboard symbols can be used to improve documentations and tutorials:

<kbd>CTRL</kbd> + <kbd>X</kbd> to cut content.
<kbd>CTRL</kbd> + <kbd>C</kbd> to copy content.
<kbd>CTRL</kbd> + <kbd>V</kbd> to paste content.

### Source

```markdown
Keyboard symbols can be used to improve documentations and tutorials:

<kbd>CTRL</kbd> + <kbd>X</kbd> to cut content.
<kbd>CTRL</kbd> + <kbd>C</kbd> to copy content.
<kbd>CTRL</kbd> + <kbd>V</kbd> to paste content.
```

# Text formatting

## Text formatting {.tabset}

### Preview

This text is **bold**.

This text is *italic*.

This text is <ins>underlined</ins>.

This text is ~~striked~~.

This text is ^superscripted^.

This text is ~subscripted~.

### Source

```markdown
This text is **bold**.

This text is *italic*.
This text is _italic_.

This text is <ins>underlined</ins>.

This text is ~~striked~~.

This text is ^superscripted^.

This text is ~subscripted~.
```

### Keyboard Shortcuts

<kbd>CTRL</kbd> + <kbd>B</kbd> for **bold**.
<kbd>CTRL</kbd> + <kbd>I</kbd> for **italic**.


# Headings

## Headings {.tabset}

### Preview

The headings are already being widely used on this page.

By using `#` the level of a heading can be defined. The less `#`, the higher (bigger) the heading is.
The syntax can be checked in the source tab.

As an alternative you can also use `=` or `-` below the header text.

### Source

```markdown
# Header of the 1. level
## Header of the 2. level
### Header of the 3. level
#### Header of the 4. level
##### Header of the 5. level
###### Header of the 6. level

####### This is not a header anymore.

This is an alternative way to define a header of the 1. Level
==

This is an alternative way to define a header of the 2. level
--
```

# Others

There are still more features that could be documented on this page.

> Wiki.js supports the complete Markdown syntax.
> As such it should be possible to use general Markdown features that are not documented on this page (e.g. embedding Youtube videos, etc.).
{.is-info}