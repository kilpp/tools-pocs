# FZF - Fuzzy Finder Guide

## Table of Contents
- [What is FZF?](#what-is-fzf)
- [Installation on Debian-based Linux](#installation-on-debian-based-linux)
- [Core Functionalities](#core-functionalities)
- [Usage Examples](#usage-examples)
- [Key Bindings](#key-bindings)
- [Advanced Features](#advanced-features)
- [Integration with Other Tools](#integration-with-other-tools)
- [Configuration](#configuration)
- [Performance Tips](#performance-tips)

## What is FZF?

**fzf** is a general-purpose command-line fuzzy finder. It's an interactive Unix filter for command-line that can be used with any list: files, command history, processes, hostnames, bookmarks, git commits, etc.

### Key Benefits
- ⚡ Extremely fast (written in Go)
- 🎯 Flexible and powerful filtering
- 🔌 Easy integration with other tools
- 💻 Works on Linux, macOS, and Windows
- 🎨 Customizable interface

## Installation on Debian-based Linux

### Method 1: Using apt (Recommended for Debian/Ubuntu)

```bash
sudo apt update
sudo apt install fzf
```

### Method 2: Using Git (Latest Version)

```bash
# Clone the repository
git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf

# Run the install script
~/.fzf/install
```

The install script will ask you:
- **Key bindings**: Enable CTRL-T, CTRL-R, and ALT-C (recommended: Yes)
- **Fuzzy completion**: Enable fuzzy auto-completion (recommended: Yes)
- **Update shell config**: Update your bashrc/zshrc (recommended: Yes)

### Method 3: Using Homebrew on Linux

```bash
# Install Homebrew if not already installed
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install fzf
brew install fzf

# Install key bindings and fuzzy completion
$(brew --prefix)/opt/fzf/install
```

### Method 4: Manual Binary Installation

```bash
# Download the latest release
wget https://github.com/junegunn/fzf/releases/download/0.46.1/fzf-0.46.1-linux_amd64.tar.gz

# Extract
tar -xzf fzf-0.46.1-linux_amd64.tar.gz

# Move to PATH
sudo mv fzf /usr/local/bin/

# Make executable
sudo chmod +x /usr/local/bin/fzf
```

### Verify Installation

```bash
fzf --version
```

## Core Functionalities

### 1. **Interactive File Finder**
Find files interactively in the current directory and subdirectories:
```bash
fzf
```

### 2. **Command-Line Integration**
Use fzf output directly in commands:
```bash
vim $(fzf)                    # Open selected file in vim
cd $(find * -type d | fzf)    # Navigate to selected directory
kill -9 $(ps aux | fzf | awk '{print $2}')  # Kill selected process
```

### 3. **Piping Data**
Pipe any list to fzf for filtering:
```bash
cat /etc/passwd | fzf
ls -la | fzf
docker ps -a | fzf
```

### 4. **Multi-Selection**
Select multiple items using `-m` flag:
```bash
fzf -m  # Use TAB to select multiple, Enter to confirm
```

### 5. **Preview Window**
Show file preview while browsing:
```bash
fzf --preview 'cat {}'
fzf --preview 'bat --color=always {}'  # With syntax highlighting
```

## Usage Examples

### Basic File Search
```bash
# Find and edit a file
vim $(fzf)

# Find and open in default app
xdg-open $(fzf)
```

### Directory Navigation
```bash
# Change to selected directory
cd $(find . -type d | fzf)

# Or use the ALT-C keybinding (if installed)
# Press ALT-C in terminal
```

### Command History Search
```bash
# Use CTRL-R to search command history
# This is automatically available after installation
```

### Git Integration
```bash
# Checkout git branch
git checkout $(git branch -a | fzf)

# View git log and show selected commit
git log --oneline | fzf --preview 'git show {1}'

# Add files interactively
git add $(git status -s | fzf -m | awk '{print $2}')
```

### Process Management
```bash
# Find and kill process
kill $(ps aux | fzf | awk '{print $2}')

# Interactive process viewer
watch -n 1 'ps aux | fzf'
```

### SSH Host Selection
```bash
# Select from known hosts
ssh $(cat ~/.ssh/config | grep "^Host " | awk '{print $2}' | fzf)
```

### Docker Operations
```bash
# Stop container
docker stop $(docker ps | fzf | awk '{print $1}')

# Remove image
docker rmi $(docker images | fzf -m | awk '{print $3}')

# Exec into container
docker exec -it $(docker ps | fzf | awk '{print $1}') bash
```

## Key Bindings

### Default Key Bindings (After Installation)

| Binding | Function |
|---------|----------|
| `CTRL-T` | Paste selected files/directories onto command-line |
| `CTRL-R` | Search command history |
| `ALT-C` | cd into selected directory |

### Interactive Mode Bindings

| Key | Action |
|-----|--------|
| `Enter` | Select and exit |
| `TAB` | Select/deselect item (multi-select mode) |
| `SHIFT-TAB` | Deselect item |
| `CTRL-J/K` | Move down/up |
| `CTRL-N/P` | Move down/up (alternative) |
| `CTRL-D/U` | Page down/up |
| `CTRL-A` | Select all (multi-select mode) |
| `CTRL-/` | Toggle preview window |
| `ESC` | Cancel and exit |

## Advanced Features

### 1. **Custom Search Command**
Change the default search command:
```bash
# Use fd instead of find
export FZF_DEFAULT_COMMAND='fd --type f'

# Use ripgrep
export FZF_DEFAULT_COMMAND='rg --files --hidden'
```

### 2. **Preview Window Options**
```bash
# Preview on the right (50% width)
fzf --preview 'cat {}' --preview-window=right:50%

# Preview at bottom
 v

# Hidden by default, toggle with CTRL-/
fzf --preview 'cat {}' --preview-window=hidden
```

### 3. **Custom Keybindings**
```bash
# Execute custom command on selected items
fzf --bind 'ctrl-e:execute(vim {})'
fzf --bind 'ctrl-y:execute-silent(echo {} | xclip -selection clipboard)'
```

### 4. **Filtering Syntax**

| Token | Match Type | Example |
|-------|------------|---------|
| `sbtrkt` | Fuzzy match | Items that match `sbtrkt` |
| `'wild` | Exact match (quoted) | Items that include `wild` |
| `^music` | Prefix exact match | Items that start with `music` |
| `.mp3$` | Suffix exact match | Items that end with `.mp3` |
| `!fire` | Inverse exact match | Items that don't include `fire` |
| `!^music` | Inverse prefix | Items that don't start with `music` |

### 5. **Color Schemes**
```bash
# Use custom colors
fzf --color=fg:#d0d0d0,bg:#121212,hl:#5f87af \
    --color=fg+:#d0d0d0,bg+:#262626,hl+:#5fd7ff \
    --color=info:#afaf87,prompt:#d7005f,pointer:#af5fff

# Monokai theme example
export FZF_DEFAULT_OPTS='--color=bg+:#293739,bg:#1B1D1E,border:#808080,spinner:#E6DB74,hl:#7E8E91,fg:#F8F8F2,header:#7E8E91,info:#A6E22E,pointer:#A6E22E,marker:#F92672,fg+:#F8F8F2,prompt:#F92672,hl+:#F92672'
```

### 6. **Height and Layout**
```bash
# Use 40% of screen height
fzf --height 40%

# Reverse layout (input at top)
fzf --reverse

# Border options
fzf --border
fzf --border=rounded
```

## Integration with Other Tools

### 1. **Vim/Neovim Integration**
Install fzf.vim plugin:
```vim
" Using vim-plug
Plug 'junegunn/fzf', { 'do': { -> fzf#install() } }
Plug 'junegunn/fzf.vim'
```

Common commands:
- `:Files` - Find files
- `:Buffers` - Switch buffers
- `:Rg` - Ripgrep search
- `:Commits` - Git commits

### 2. **Bash Functions**

Add to `~/.bashrc`:
```bash
# fd - cd to selected directory
fd() {
  local dir
  dir=$(find ${1:-.} -path '*/\.*' -prune -o -type d -print 2> /dev/null | fzf +m) &&
  cd "$dir"
}

# fh - search command history
fh() {
  eval $( ([ -n "$ZSH_NAME" ] && fc -l 1 || history) | fzf +s --tac | sed 's/ *[0-9]* *//')
}

# fkill - kill process
fkill() {
  local pid
  pid=$(ps -ef | sed 1d | fzf -m | awk '{print $2}')
  if [ "x$pid" != "x" ]; then
    echo $pid | xargs kill -${1:-9}
  fi
}
```

### 3. **Zsh Integration**

Add to `~/.zshrc`:
```zsh
# Use fzf for directory history
# Press CTRL-G to activate
bindkey -s '^G' 'cd $(dirs -v | fzf | awk "{print \$2}")^M'
```

### 4. **tmux Integration**
```bash
# fzf-tmux: Run fzf in tmux split pane
fzf-tmux -d 40%  # 40% height split at bottom
fzf-tmux -r 50%  # 50% width split on right
```

## Configuration

### Environment Variables

```bash
# Default search command
export FZF_DEFAULT_COMMAND='fd --type f --hidden --follow --exclude .git'

# Default options
export FZF_DEFAULT_OPTS='--height 40% --layout=reverse --border'

# CTRL-T options
export FZF_CTRL_T_COMMAND="$FZF_DEFAULT_COMMAND"
export FZF_CTRL_T_OPTS="--preview 'bat --color=always --line-range :500 {}'"

# ALT-C options
export FZF_ALT_C_COMMAND='fd --type d --hidden --follow --exclude .git'
export FZF_ALT_C_OPTS="--preview 'tree -C {} | head -200'"

# CTRL-R options
export FZF_CTRL_R_OPTS="--preview 'echo {}' --preview-window down:3:hidden:wrap --bind '?:toggle-preview'"
```

Add these to your `~/.bashrc` or `~/.zshrc`.

### Complete Configuration Example

Add to `~/.bashrc`:
```bash
# FZF Configuration
export FZF_DEFAULT_COMMAND='fd --type f --hidden --follow --exclude .git --exclude node_modules'
export FZF_DEFAULT_OPTS='
  --height 40% 
  --layout=reverse 
  --border 
  --inline-info
  --color=fg:#d0d0d0,bg:#121212,hl:#5f87af
  --color=fg+:#d0d0d0,bg+:#262626,hl+:#5fd7ff
  --color=info:#afaf87,prompt:#d7005f,pointer:#af5fff
  --color=marker:#af5fff,spinner:#af5fff,header:#87afaf
  --preview-window=:hidden
  --bind "?:toggle-preview"
  --bind "ctrl-a:select-all"
  --bind "ctrl-y:execute-silent(echo {} | xclip -selection clipboard)"
'

export FZF_CTRL_T_COMMAND="$FZF_DEFAULT_COMMAND"
export FZF_CTRL_T_OPTS="--preview 'bat --style=numbers --color=always --line-range :500 {}'"

export FZF_ALT_C_COMMAND='fd --type d --hidden --follow --exclude .git'
export FZF_ALT_C_OPTS="--preview 'tree -C {} | head -100'"
```

## Performance Tips

### 1. **Use Fast Search Tools**
- Use `fd` instead of `find`
- Use `rg` (ripgrep) instead of `grep`
- Use `bat` instead of `cat` for preview

Install them:
```bash
sudo apt install fd-find ripgrep bat
```

### 2. **Limit Preview Size**
```bash
fzf --preview 'bat --color=always --line-range :500 {}'
```

### 3. **Exclude Unnecessary Directories**
```bash
export FZF_DEFAULT_COMMAND='fd --type f --hidden --follow --exclude .git --exclude node_modules --exclude .cache'
```

### 4. **Use Binary for Better Performance**
Install the binary version instead of using apt if you need the latest performance optimizations.

## Common Use Cases - Quick Reference

```bash
# Find and edit file
vim $(fzf)

# Find and cd to directory
cd $(fd -t d | fzf)

# Search file contents
rg --line-number . | fzf --delimiter : --preview 'bat --color=always {1} --highlight-line {2}'

# Git checkout branch
git checkout $(git branch | fzf)

# Git show commit
git log --oneline | fzf --preview 'git show {1}'

# Kill process
kill $(ps aux | fzf | awk '{print $2}')

# Open URL from history
xdg-open $(cat ~/.bash_history | grep http | fzf)

# Edit recent files
vim $(ls -t | fzf)

# Select multiple files and delete
rm $(fzf -m)
```

## Troubleshooting

### fzf not found after installation
```bash
# Reload shell configuration
source ~/.bashrc
# or
source ~/.zshrc
```

### Key bindings not working
```bash
# Re-run the install script with key bindings
~/.fzf/install --key-bindings --completion --update-rc
```

### Preview not showing
```bash
# Install bat for better preview
sudo apt install bat

# On Debian/Ubuntu, bat might be installed as batcat
# Create alias
mkdir -p ~/.local/bin
ln -s /usr/bin/batcat ~/.local/bin/bat
```

## Resources

- **Official Repository**: https://github.com/junegunn/fzf
- **Documentation**: https://github.com/junegunn/fzf/blob/master/README.md
- **Wiki**: https://github.com/junegunn/fzf/wiki
- **Examples**: https://github.com/junegunn/fzf/wiki/examples

## Conclusion

fzf is an incredibly powerful and versatile tool that can significantly improve your command-line productivity. Start with basic usage and gradually incorporate more advanced features as you become comfortable. The key is to experiment and find workflows that suit your needs.

Happy fuzzy finding! 🔍
