
# Neovim Configuration Reference

This document explains my Neovim configuration, keybindings, plugins, and things I want to add later.

---

## Directory Structure

My Neovim configuration is organized like this:

~/.config/nvim/
├── init.lua
└── lua/
    ├── rodney/
    │   ├── set.lua
    │   └── remap.lua
    └── plugins/
        ├── fugitive.lua
        ├── harpoon.lua
        ├── nightfox.lua
        ├── telescope.lua
        ├── treesitter.lua
        └── undotree.lua

### init.lua

The main entry point for Neovim.

It loads my personal configuration and initializes Lazy.nvim.

    require("rodney.set")
    require("rodney.remap")

    require("lazy").setup("plugins")

---

# Configuration Files

## set.lua

Contains general Neovim options.

Examples:

    vim.opt.nu = true
    vim.opt.relativenumber = true

This enables both absolute and relative line numbers.

The current line shows its actual line number while surrounding lines show their distance from the current line.

For example:

    3
    2
    1
    20  <- current line
    1
    2
    3

Other settings control indentation, swap files, undo history, searching, scrolling, and terminal colors.

---

## remap.lua

Contains my personal keybindings.

The leader key is Space:

    vim.g.mapleader = " "

Therefore:

    <leader>pv

means:

    Space + p + v

---

# Keybindings

## File Explorer

    vim.keymap.set("n", "<leader>pv", vim.cmd.Ex)

### Space + p + v

Opens Neovim's built-in file explorer, netrw.

---

## Move Selected Lines

    vim.keymap.set("v", "J", ":m '>+1<CR>gv=gv")
    vim.keymap.set("v", "K", ":m '<-2<CR>gv=gv")

These work in Visual mode.

### J

Move the selected lines down.

### K

Move the selected lines up.

The selection is restored after moving.

---

## Join Lines

    vim.keymap.set("n", "J", "mzJ`z")

### J

Join the current line with the line below it while keeping the cursor position stable.

---

## Scrolling

    vim.keymap.set("n", "<C-d>", "<C-d>zz")
    vim.keymap.set("n", "<C-u>", "<C-u>zz")

### Ctrl + d

Scroll down half a page and center the cursor.

### Ctrl + u

Scroll up half a page and center the cursor.

The zz keeps the cursor centered on the screen.

---

## Search Navigation

    vim.keymap.set("n", "n", "nzzzv")
    vim.keymap.set("n", "N", "Nzzzv")

### n

Go to the next search result and center it.

### N

Go to the previous search result and center it.

---

## Paste Without Losing Yanked Text

    vim.keymap.set("x", "<leader>p", "\"_dP")

### Space + p

Paste over selected text without overwriting the current yank.

The deleted text is sent to the black-hole register:

    "_d

This means the text I previously yanked remains available.

---

## System Clipboard

    vim.keymap.set("n", "<leader>y", "\"+y")
    vim.keymap.set("v", "<leader>y", "\"+y")
    vim.keymap.set("n", "<leader>Y", "\"+Y")

### Space + y

Yank to the system clipboard.

### Visual mode + Space + y

Copy the selected text to the system clipboard.

### Space + Y

Yank from the cursor to the end of the line to the system clipboard.

The "+ specifies the system clipboard register.

---

## Delete to System Clipboard

    vim.keymap.set("n", "<leader>d", "\"+d")
    vim.keymap.set("v", "<leader>d", "\"+d")

### Space + d

Delete text while putting it into the system clipboard.

---

## Tmux Sessionizer

    vim.keymap.set("n", "<C-f>", "<cmd>silent !tmux neww tmux-sessionizer<CR>")

### Ctrl + f

Opens a new tmux window and runs:

    tmux neww tmux-sessionizer

This requires tmux and the tmux-sessionizer command to be installed/configured.

---

## Format with LSP

    vim.keymap.set("n", "<leader>f", function()
        vim.lsp.buf.format()
    end)

### Space + f

Formats the current file using the attached language server.

NOTE: LSP has currently been removed from my configuration. This mapping will need to be restored when LSP is set up again.

---

## Quickfix List

    vim.keymap.set("n", "<C-k>", "<cmd>cnext<CR>zz")
    vim.keymap.set("n", "<C-j>", "<cmd>cprev<CR>zz")

### Ctrl + k

Go to the next quickfix item.

### Ctrl + j

Go to the previous quickfix item.

The zz centers the resulting line.

---

## Location List

    vim.keymap.set("n", "<leader>k", "<cmd>lnext<CR>zz")
    vim.keymap.set("n", "<leader>j", "<cmd>lprev<CR>zz")

### Space + k

Go to the next location-list item.

### Space + j

Go to the previous location-list item.

---

## Search and Replace

    vim.keymap.set("n", "<leader>s", [[:%s/\<<C-r><C-w>\>/<C-r><C-w>/gI<Left><Left><Left>]])

### Space + s

Starts a search-and-replace command using the word currently under the cursor.

It prepares a command similar to:

    :%s/<word>/<word>/gI

The word under the cursor is automatically inserted into both the search and replacement fields.

The replacement can then be edited before executing the command.

### Breakdown

    :%s/

Search and replace throughout the entire file.

    \<word\>

Match the complete word.

    g

Replace every occurrence on each line.

    I

Ignore case.

---

## Make Current File Executable

    vim.keymap.set("n", "<leader>x", "<cmd>!chmod +x %<CR>", { silent = true })

### Space + x

Runs:

    chmod +x <current-file>

This makes the current file executable.

This is particularly useful for shell scripts.

---

# Plugins

My plugins are managed with Lazy.nvim.

Plugin configuration files are stored in:

    ~/.config/nvim/lua/plugins/

Each plugin can have its own configuration file.

For example:

    lua/plugins/harpoon.lua

contains the Harpoon plugin and its configuration.

This keeps each plugin's configuration self-contained.

---

# Lazy.nvim

Lazy.nvim is my Neovim plugin manager.

It installs, updates, removes, and loads my plugins.

Useful commands:

    :Lazy

Open the Lazy interface.

    :Lazy sync

Install/update/remove plugins to match my configuration.

    :Lazy update

Update installed plugins.

    :Lazy clean

Remove plugins that are installed but no longer declared in my configuration.

---

# Nightfox

Nightfox is my colorscheme.

It controls the appearance and colors of Neovim.

My configuration also makes the normal background transparent.

---

# Telescope

Telescope is a fuzzy finder.

It allows me to quickly search for files, text, buffers, and other things.

My mappings include:

### Space + p + f

Find files.

### Ctrl + p

Find Git-tracked files.

### Space + p + s

Search for text in the project.

Mental model:

    Telescope = "Find something."

---

# Harpoon

Harpoon is used for quickly switching between a small number of important files.

Instead of searching for a file repeatedly with Telescope, I can mark important files and jump directly between them.

My mappings are:

### Space + a

Add the current file to Harpoon.

### Ctrl + e

Open the Harpoon menu.

### Ctrl + h

Jump to Harpoon file 1.

### Ctrl + t

Jump to Harpoon file 2.

### Ctrl + n

Jump to Harpoon file 3.

### Ctrl + s

Jump to Harpoon file 4.

Mental model:

    Telescope = "Find me a file."

    Harpoon = "Take me to one of my important files."

---

# Undotree

Undotree provides a visual representation of Neovim's undo history.

My mapping is:

### Space + u

Open/close Undotree.

It allows me to visualize different branches of my editing history instead of only using u and Ctrl + r.

---

# Fugitive

Fugitive is a Git integration plugin for Vim/Neovim.

My mapping is:

### Space + g + s

Runs:

    :Git

This opens Fugitive's Git interface.

Fugitive allows me to interact with Git from inside Neovim.

---

# Treesitter

nvim-treesitter provides syntax parsing for programming languages.

It allows Neovim to understand the structure of source code instead of treating everything as plain text.

It is useful for things such as:

- Syntax highlighting
- Code structure
- Folding
- Indentation
- Other syntax-aware features

Mental model:

    Treesitter = "What is the structure of this code?"

---

# Markdown Preview

markdown-preview enables realtime markdown previews through `:MarkdownPreview` command when viewing a Markdown file.  

---

# LSP

LSP stands for Language Server Protocol.

Neovim has a built-in LSP client.

A language server provides language-specific programming intelligence.

Examples include:

    Python -> Pyright / basedpyright
    Bash   -> bash-language-server
    Lua    -> lua-language-server

LSP can provide:

- Go to definition
- Find references
- Autocomplete
- Diagnostics
- Documentation
- Rename
- Code actions
- Formatting

LSP is currently NOT installed/configured in my setup.

---

# TODO

## LSP

Set up LSP using Neovim's built-in LSP client.

Possible components:

    Neovim built-in LSP
            |
            +-- nvim-lspconfig
            |
            +-- language servers
                  |
                  +-- Python
                  +-- Bash
                  +-- Lua

Do not automatically install Node/npm just for LSP.

Investigate using a Python-native installation for the Python language server if appropriate.

Add useful LSP keybindings such as:

    gd          Go to definition
    gr          Find references
    K           Hover documentation
    <leader>rn  Rename
    <leader>ca  Code action

Also restore:

    vim.keymap.set("n", "<leader>f", function()
        vim.lsp.buf.format()
    end)

once LSP is configured.

---

## More Plugins

Possible future plugins/features to investigate:

- Completion
- Git signs
- Which-key
- Formatting
- Linting
- Debugging
- More Telescope extensions

---

# Quick Keybinding Reference

| Key | Action |
|---|---|
| Space pv | File explorer |
| J (Visual) | Move selection down |
| K (Visual) | Move selection up |
| J | Join lines |
| Ctrl d | Scroll down and center |
| Ctrl u | Scroll up and center |
| n | Next search result and center |
| N | Previous search result and center |
| Space p (Visual) | Paste without replacing yank |
| Space y | Yank to system clipboard |
| Space Y | Yank line to system clipboard |
| Space d | Delete to system clipboard |
| Ctrl f | Tmux sessionizer |
| Space f | Format with LSP (currently disabled) |
| Ctrl k | Next quickfix item |
| Ctrl j | Previous quickfix item |
| Space k | Next location item |
| Space j | Previous location item |
| Space s | Search and replace |
| Space x | Make file executable |
| Space a | Harpoon: add file |
| Ctrl e | Harpoon menu |
| Ctrl h | Harpoon file 1 |
| Ctrl t | Harpoon file 2 |
| Ctrl n | Harpoon file 3 |
| Ctrl s | Harpoon file 4 |
| Space u | Toggle Undotree |
| Space g s | Fugitive Git |

---

# Important Concepts

## Leader

The leader key is Space:

    vim.g.mapleader = " "

So:

    <leader>x

means:

    Space + x

## Modes

Neovim has different modes.

The most important ones are:

    Normal mode  -> navigating and commands
    Insert mode  -> typing text
    Visual mode  -> selecting text
    Command mode -> entering : commands

When a mapping contains:

    vim.keymap.set("n", ...)

the "n" means Normal mode.

    vim.keymap.set("v", ...)

means Visual mode.

    vim.keymap.set("x", ...)

means Visual mode for character/line/block selections.

---

# Overall Mental Model

My configuration has four main parts:

    ~/.config/nvim/
    │
    ├── init.lua
    │
    └── lua/
        │
        ├── rodney/
        │   ├── set.lua
        │   └── remap.lua
        │
        └── plugins/
            ├── nightfox.lua
            ├── telescope.lua
            ├── treesitter.lua
            ├── harpoon.lua
            ├── undotree.lua
            └── fugitive.lua

The roles are:

    set.lua
        ↓
    Neovim settings/options

    remap.lua
        ↓
    My personal keyboard shortcuts

    plugins/
        ↓
    Plugin installation + plugin-specific configuration

    LSP
        ↓
    Language-specific programming intelligence
        ↓
    Currently a TODO

The overall philosophy is:

    My configuration
        ↓
    Simple personal settings in rodney/
        ↓
    Plugin-specific settings in plugins/
        ↓
    Lazy.nvim manages the plugins
