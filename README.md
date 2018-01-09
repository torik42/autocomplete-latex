[See this on atom.io](https://atom.io/packages/autocomplete-latex)
# autocomplete-latex

## Table of contents

- [About](#about)
- [Installation](#installation)
- [Usage](#usage)
  - [Requirements](#requirements)
  - [Setup](#setup)
  - [In document](#in-document)
- [Configuration](#configuration)
  - [Settings](#settings)
  - [Adding completions](#adding-completions)

## About
This package is intended to be used with [Atom](https://atom.io).

- **Note**: This package is compatible with [`latex-autocomplete`](https://atom.io/packages/latex-autocomplete) and does not necessarily replace it's functionality. They perform different types of autocompleting. Both can be enabled simultaneously without conflict.

It provides a range of customisable "completions" that appear when typing a command. When one is selected, it will expand to a predefined snippet of text that can then be used like normal.

The benefits of this package include reduced [boilerplate](https://en.wikipedia.org/wiki/Boilerplate_code) code and fewer errors (especially for LaTeX beginners). For example, one of the default completions prefixes is `\figure`. Expanding this results in the following text insertion:
```latex
\begin{figure}[]
  \centering
  \includegraphics{}
  \caption{}
  \label{}
\end{figure}
```
with the cursor placing itself between the first optional brackets. Pressing `tab` then cycles through the bracket pairs, enabling quick and easy figure insertion.

This functionality may already be present as snippets from other packages. However, there are certain things this package can do that are difficult or impossible to do with snippets. Some are aesthetic, but others include better scope parsing and customisability.

## Why use this package?
Presumably, you're here because you want to be able to autocomplete common commands you use. Yes, snippets can do this, but they can be difficult to remember, especially if used infrequently. `autocomplete-snippets` is a handy package that displays most snippets in a popup as you type, but it has one major flaw when it comes to LaTeX documents: it doesn't support punctuation.

Most of what you type in LaTeX is just regular words. It gets annoying when you're typing, and a popup menu appears every other word. This package only shows suggestions when a `\` is typed; this makes completions easy to access but kept out of the way during normal use.

## Installation
To install, run `apm install autocomplete-latex` or find it in Atom's builtin package manager.

## Usage
### Requirements

- The package [`autocomplete-plus`](https://atom.io/packages/autocomplete-plus) be activated. This is a builtin package, so no additional download required.

- A grammar scoping package for LaTeX. These are the ones titled `language-something`. The most popular one is [`language-latex`](https://atom.io/packages/language-latex), but [my own](https://atom.io/packages/language-latex2e) will also work. I haven't tested with any others that scope LaTeX, but they should work if they follow established conventions.
  - Note: If you have syntax highlighting setup, you probably already have one installed.

### Setup
You don't need to configure anything to get started; this package will work out of the box to provide completions for common patterns such as `\begin`, `\usepackage`, `\frac`, etc.

### In document
As mentioned above, completions will automatically appear when typing. These suggestions are filtered and ranked according to the prefix, which is defined as a `\` followed by any number of letters (`\\[a-zA-Z]*` for you [regex](https://www.marksanborn.net/howto/learning-regular-expressions-for-beginners-the-basics/) fans) up until the cursor. If any non-letters are present (eg. number or space) it will no longer show the completions and will wait until a valid prefix is reached again.

  - **Note**: Two special cases where it will also activate are after an `!` and an `@`. This is to support [magic comment](https://tex.stackexchange.com/questions/78101/when-and-why-should-i-use-tex-ts-program-and-tex-encoding) completions and markdown-like citations.

The current scope also affects which completions appear, so you'll only see commands like `\frac` as an option when in math mode.

 - **Tip**: the completions will appear if the current prefix is an ordered subset of the potential completion. This means that while you _could_ type `\subsub` to get the `\subsubsection` completion, you could just type `\sbb` instead.

If something unexpected is occurring, check your [settings](#settings). Make sure all the [requirements](#requirements) are also met. If the issue persists, [open an issue](https://github.com/Aerijo/autocomplete-latex/issues) on the repo page and I'll try to help. It's still a young package, so I wouldn't expect it to be perfect (yet :wink:).

To define your own completions, see [Adding completions](#adding-completions)

## Configuration
### Settings
In the settings view, there are ~~several~~ many options available to customise how this package works. If changing a setting does not appear to work, try restarting Atom.

#### User completions path
Use this to provide the path to your own completions. Ensure it is an absolute path, and that you include the file extension as well. More info about what this file can be is found in [Adding completions](#adding-completions) and `./docs/`.

#### Enable default completions
This option allows you to disable the default LaTeX completions. This may be preferable if you have a highly customised setup. Note that user completions will override the default completions if both share the same display text.

#### Enable builtin provider
Use this to allow the completions provided by `autocomplete-plus`. These are not controlled by this package, so settings here will not affect them. They are the words taken from open buffers that appear as you type.

#### Enable citation completions
This package supports markdown-like syntax for citations. When enabled, typing `@` followed by non-space characters will show completions scraped from the `.bib` file, if it can be located. The file path must be given somewhere in the current file for this to work, and the magic comment `% !TEX bib = ...` is supported.

#### Minimum prefix length
Determines how long the prefix must be until suggestions will appear. Default value is that of `autocomplete-plus`. Making it greater will reduce 'noise', but decrease effectiveness (because lessening typing is what this is all about; well, that and reducing typos).

#### Disabled scopes
A list of scopes where you do not want to see completions from this package. To know which scopes to use, run the command `editor:log-cursor-scope` in the command palette. The notification that pops up will list the current scopes at the cursor. For example, doing this in a commented section might give the following scopes:
- `text.tex.latex`
- `comment.line.percentage.latex`

In order to disable completions in comments, you can set the value of this setting to `.comment` (note the starting `.`; it's important!). This will look for whether any of the scopes you're in start with `comment` (like in the example), and suppress completions if true.

You can be as precise as you like; a value of `.string.other.math.inline` will suppress when in inline math mode, whereas `.string.other.math.display` will suppress it in display math mode. You can also require multiple scopes to be true by `space` separating the scopes. Eg.
- `.suppress.for.this.scope .suppress.for.that.scope`

will suppress completions when both scopes are present. Similarly, `comma` separated groups will suppress completions if at least one matches. Eg.
- `.comment, .string.other.math.display .string.other.math.inline`

will disable completions if the current scope is part of a comment _or_ in math mode. (Note: if you want to disable all math snippets, `.string.other.math` will disable for both inline _and_ display).

#### Citation format
The format for what `@...` should be replaced with. All backslashes must be doubled up, and a single `$` must be somewhere to represent where the citation text will go.

#### :warning: Completion regex
If you don't know what [`regex`](https://www.marksanborn.net/howto/learning-regular-expressions-for-beginners-the-basics/) is, don't touch this setting. If you change it, make sure to put it back to the default. You've been warned.

If you know what you're doing, this setting determines which characters are considered a valid prefix to the completion. The `$` represents the current cursor position, and it can look back as far as the beginning of the line. Backslashes do __not__ need to be doubled up.

#### :warning: Citation completion regex
Same as above, but for `@...` completions.


### Adding completions
**Not stable**: Look in docs for current format.
- Note: I'm still not satisfied with the current format. Expect it to continue to change to make organising related sections better.
  - Eg. I want to be able to make groups that can be enabled/disabled easily.

- Also, the file can be `.js` as well, so long as it's export is an object with the same properties of the `.json`. Eg.

```js
// in file userCompletions.js
const completions = {};
/*
  some javascript code
*/
completions[".text.tex.latex"] = {
  "snippet": [
    {
      "displayText": "\\myCompletion",
      "snippet": "\\\\expands to string of text and places cursor here -> $1 <-",
      "description": "Example completion"
    },
    {
      "comment": "Though not recommended, type can be explicitly overridden",
      "type": "constant",
      "displayText": "\\LaTeX",
      "snippet": "\\{\\\\LaTeX\\}$1",
      "description": "Builtin LaTeX command + protective braces"
    }
  ]
}
/*
  other javascript code
*/
module.exports = completions
```
