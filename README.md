# Dotfiles

A collection of unix based dotfiles including:

- A fast, customizable, pure-shell, asynchronous Git prompt for Zsh.
- Git configuration files
- Psql configuration file

## Installation

```bash
# Clone the repository
mkdir -p ~/.dotfiles
git clone --depth=1 https://github.com/jordanell/dotfiles ~/.dotfiles

# Install the prompt
echo "source ~/.dotfiles/git-prompt.zsh" >> .zshrc

# Optional: install an example configuration
echo "source ~/.dotfiles/prompts/default.zsh" >> .zshrc

# Install all of the configuration files
ln -s ~/.dotfiles/gitconfig ~/.gitconfig
ln -s ~/.dotfiles/gitignore ~/.gitignore
ln -s ~/.dotfiles/gitmessage ~/.gitmessage
ln -s ~/.dotfiles/psqlrc ~/.psqlrc
```

## Prompt Structure

The structure of the prompt (in the default configuration) is the following:

```
[<branch_name><upstream><tracking_status>|<local_status>]
```

* `branch_name`: Name of the current branch or commit hash if HEAD is detached. When in 'detached HEAD' state, the
    `branch_name` will be prefixed with a colon `:` to indicate that it is actually a hash and not a branch name.
* `upstream`: Name of the remote branch if it exist.
    Must be enabled explicitly (see [Enable remote branch info](#enable-remote-branch-info)).
* `tracking_status`:
    * `↑n`: ahead of remote by `n` commits
    * `↓n`: behind remote by `n` commits
    * `↓m↑n`: branches diverged; other by `m` commits, yours by `n` commits
* `local_status`:
    * `✔`: repository is clean
    * `✖n`: there are `n` unmerged files
    * `●n`: there are `n` staged files
    * `✚n`: there are `n` unstaged and changed files
    * `…n`: there are `n` untracked files
    * `⚑n`: there are `n` entries on the stash (disabled by default)

## Prompt Customization

Unlike other popular prompts this prompt does not use `promptinit`, which gives you the flexibility to build your own prompt from scratch. You can build a custom prompt by setting the `PROMPT` variable in your `.zshrc` after souring the `git-prompt.zsh`. And you should use `'$(gitprompt)'` in your `PROMPT` to get the Git prompt. You must set your `PROMPT` with **single quotes**, not double quotes, otherwise the Git prompt will not update properly. Some example `PROMPT` configurations are given below. You can find more information on how to configure the `PROMPT` in [Zsh's online documentation](http://zsh.sourceforge.net/Doc/Release/Prompt-Expansion.html) or the `zshmisc` manpage, section "SIMPLE PROMPT ESCAPES".

### Examples

You can try these configurations by souring the `.zsh` files from the [examples](./examples) directory. After you have found a configuration that you like, source it in your `.zshrc`.

#### Default

```zsh
# Preview:
# ../git-prompt.zsh [master↑1|●1✚1…1] ❯❯❯

source prompts/default.zsh
```
#### Compact

```zsh
# ../git-prompt.zsh master↑1|●1✚1…1❯

source prompts/compact.zsh
```

#### Multi-line prompt

```zsh
# Preview:
# ┏╸130 · ~/workspace/git-prompt.zsh · ⎇ master↑1 ‹●1✚1…1›
# ┗╸❯❯❯

source prompts/multiline.zsh
```

### Appearance
The appearance of the prompt can be adjusted by changing the variables that start with `ZSH_THEME_GIT_PROMPT_`. Note that some of them are named differently than in the original Git prompt by Olivier Verdier.

You can preview your configuration by setting the `ZSH_THEME_GIT_PROMPT_*` variables in a running shell. But remember to save them in your `.zshrc` after you tweaked them to your liking!
Example snippet from `.zshrc`:

```zsh
# Theming variables for primary prompt
ZSH_THEME_GIT_PROMPT_PREFIX="["
ZSH_THEME_GIT_PROMPT_SUFFIX="] "
ZSH_THEME_GIT_PROMPT_SEPARATOR="|"
ZSH_THEME_GIT_PROMPT_DETACHED="%{$fg_bold[cyan]%}:"
ZSH_THEME_GIT_PROMPT_BRANCH="%{$fg_bold[magenta]%}"
ZSH_THEME_GIT_PROMPT_UPSTREAM_SYMBOL="%{$fg_bold[yellow]%}⟳ "
ZSH_THEME_GIT_PROMPT_UPSTREAM_PREFIX="%{$fg[red]%}(%{$fg[yellow]%}"
ZSH_THEME_GIT_PROMPT_UPSTREAM_SUFFIX="%{$fg[red]%})"
ZSH_THEME_GIT_PROMPT_BEHIND="↓"
ZSH_THEME_GIT_PROMPT_AHEAD="↑"
ZSH_THEME_GIT_PROMPT_UNMERGED="%{$fg[red]%}✖"
ZSH_THEME_GIT_PROMPT_STAGED="%{$fg[green]%}●"
ZSH_THEME_GIT_PROMPT_UNSTAGED="%{$fg[red]%}✚"
ZSH_THEME_GIT_PROMPT_UNTRACKED="…"
ZSH_THEME_GIT_PROMPT_STASHED="%{$fg[blue]%}⚑"
ZSH_THEME_GIT_PROMPT_CLEAN="%{$fg_bold[green]%}✔"

# Theming variables for the secondary prompt
ZSH_THEME_GIT_PROMPT_SECONDARY_PREFIX=""
ZSH_THEME_GIT_PROMPT_SECONDARY_SUFFIX=""
ZSH_THEME_GIT_PROMPT_TAGS_SEPARATOR=", "
ZSH_THEME_GIT_PROMPT_TAGS_PREFIX="🏷 "
ZSH_THEME_GIT_PROMPT_TAGS_SUFFIX=""
ZSH_THEME_GIT_PROMPT_TAG="%{$fg_bold[magenta]%}"
source path/to/git-prompt.zsh
```

### Enable remote branch info

The prompt will show information about the remote branch, if `ZSH_GIT_PROMPT_SHOW_UPSTREAM` is set to `full` or `symbol`. The `full` option will print the full remote branch name enclosed by `ZSH_THEME_GIT_PROMPT_UPSTREAM_PREFIX` and `ZSH_THEME_GIT_PROMPT_UPSTREAM_SUFFIX`. The `symbol` option prints only `ZSH_THEME_GIT_PROMPT_UPSTREAM_SYMBOL`.

### Show number of stash entries

The number of stash entries will be shown if `ZSH_GIT_PROMPT_SHOW_STASH` is set. Enabling this will execute another Git command every time a new prompt is shown! To enable stash entries add the following line to your `.zshrc`:

```bash
ZSH_GIT_PROMPT_SHOW_STASH=1
```

### Force blank

Since the prompt is asynchronous by default, the Git status updates slightly delayed. This has the benefit that the prompt will always be responsive even if the repository is huge and/or your disk is slow. But it also means that the old status will be displayed for some time. You can force the prompt to blank out instead of displaying a potentially outdated status, but be warned that this will probably increase flickering. Set the following variable in your `.zshrc` to enable this behavior:

```bash
ZSH_GIT_PROMPT_FORCE_BLANK=1
```

### Disable async

If you are not happy with the asynchronous behavior, you can disable it altogether. But be warned that this can make your shell painfully slow if you enter large repositories or if your disk is slow. Set the following variable in your `.zshrc` **before** sourcing the `git-prompt.zsh` to enable this behavior.

```bash
ZSH_GIT_PROMPT_NO_ASYNC=1
```

`ZSH_GIT_PROMPT_NO_ASYNC` cannot be adjusted in a running shell, but only in your `.zshrc`.
