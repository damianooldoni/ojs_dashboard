# Fix for Quarto Extension Error

## Problem

When running `quarto render` or `quarto preview`, the build would fail with:

```
ERROR: You must use the .qmd extension for documents with executable code.

Stack trace:
    at Object.execute (file:///C:/Program Files/Positron/resources/app/quarto/bin/quarto.js:41073:23)
    ...
```

## Root Cause

The documentation files `BEFORE_AFTER.md` and `REACTIVITY_FIX.md` contained code blocks marked with triple backticks followed by `{ojs}`, like this pattern: ` ``` ` + `{ojs}`.

This syntax tells Quarto that the code block contains **executable** OJS code. When Quarto encounters executable code blocks in a file, it requires the file to have a `.qmd` (Quarto Markdown) extension, not a plain `.md` extension.

However, these documentation files were not meant to execute code - they were showing **example** code snippets to explain how the dashboard works. The code blocks were inside outer code fences to display them as literal text.

Despite being nested within outer fences, Quarto was still detecting the inner markers with `{ojs}` as executable code directives.

## Solution

Changed all code block markers from `{ojs}` to `javascript` in the documentation files. This tells Quarto to:
1. Treat the code as **non-executable** JavaScript for syntax highlighting
2. Not require the `.qmd` extension
3. Still display the code properly with syntax highlighting

### Files Modified

1. **BEFORE_AFTER.md**: Changed 6 instances from `{ojs}` to `javascript`
2. **REACTIVITY_FIX.md**: Changed 6 instances from `{ojs}` to `javascript`

## Verification

After the fix:

```bash
$ quarto render
[1/6] index.qmd
[2/6] VISUAL_GUIDE.md
[3/6] TESTING_GUIDE.md
[4/6] FIX_DETAILS.md
[5/6] REACTIVITY_FIX.md
[6/6] BEFORE_AFTER.md

Output created: docs/index.html
```

✅ All files render successfully without errors
✅ The main dashboard (index.qmd) still works correctly
✅ Documentation files are properly formatted with syntax highlighting
✅ No "must use .qmd extension" errors

## Why This Works

- `.qmd` files are for **executable** Quarto documents (with R, Python, OJS, etc. code)
- `.md` files are for **plain** Markdown documentation
- Code blocks with `{language}` syntax (like `{ojs}`, `{r}`, `{python}`) indicate executable code
- Code blocks with just the language name (like `javascript`, `python`, `r`) are for display only
- Since our documentation files only needed to **show** the code (not execute it), changing from `{ojs}` to `javascript` was the correct fix

## Alternative Solutions (Not Used)

1. **Rename files to .qmd**: Would work but unnecessary - these files don't need to execute code
2. **Escape the code blocks differently**: More complex and less readable
3. **Use verbatim blocks**: Would lose syntax highlighting

The chosen solution maintains readability while fixing the build error.
