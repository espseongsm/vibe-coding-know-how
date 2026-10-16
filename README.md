# Use Personal and Work Claude Accounts Side by Side on macOS

Switching between a personal and a work account gets tedious fast. If you **split the CLI config folder and the Desktop login data folder**, you can run both accounts independently on the same Mac.

This guide is for **macOS and zsh**. The personal account uses the default profile. The work account gets the name `work`.

| Purpose | How to launch after setup |
| --- | --- |
| Personal CLI | `claude` |
| Work CLI | `claude-work` |
| Personal Desktop | Launch Claude from Finder or Spotlight |
| Work Desktop | `claude-work-app` |

## 1. Install the CLI and Desktop app

If you do not have the CLI yet, run the [official install command](https://code.claude.com/docs/en/setup) in a terminal.

```zsh
curl -fsSL https://claude.ai/install.sh | bash
```

Download Desktop from the [official download page](https://claude.com/download) and install it to `/Applications/Claude.app`. If both are already installed, go to the next step.

## 2. Split the CLI accounts

Open your shell config file. If it does not exist, saving it creates it.

```zsh
nano "$HOME/.zshrc"
```

Add this line.

```zsh
alias claude-work='CLAUDE_CONFIG_DIR="$HOME/.claude-work" claude'
```

The personal CLI uses the default `~/.claude` folder. The work CLI uses `~/.claude-work`. The alias sets `CLAUDE_CONFIG_DIR` for the work command only. See the [official environment variable reference](https://code.claude.com/docs/en/env-vars#variables).

In nano, press `Ctrl+O` → `Enter` to save, then `Ctrl+X` to exit. Apply the change, then log in to each account.

```zsh
source "$HOME/.zshrc"
claude auth login       # log in with your personal account
claude-work auth login  # log in with your work account
```

Pick the matching account on the browser sign-in page. After that, run `claude` and `claude-work` in separate terminals.

## 3. Split the Desktop accounts

Open the regular Claude app and log in with your **personal account**. Launch the work app with a separate data folder.

Add this function to the same `~/.zshrc`. `claude-work-app` is a launch command you define yourself.

```zsh
claude-work-app() {
  open -n -a "/Applications/Claude.app" \
    --env "CLAUDE_CONFIG_DIR=$HOME/.claude-work" \
    --args \
    --user-data-dir="$HOME/Library/Application Support/Claude-Work"
}
```

Save, apply the change, and launch the work app.

```zsh
source "$HOME/.zshrc"
claude-work-app
```

In the new window, log in with your **work account**. CLI and Desktop logins are separate, so log in to each one.

`--user-data-dir` separates the work app's login data. `--env` passes the work config path to local Code. This Desktop split relies on launch options, so it is not an officially supported multi-profile setup. See [a public launcher for reference](https://github.com/bartekczyz/ai-profiles/blob/main/apps/ai-profiles/src-tauri/src/launch.rs).

**The function opens a new app instance every time you call it.** If the work app is already open, use the existing window.

## 4. Check which account is active

In the CLI, check the login email with these commands. See the [auth command reference](https://code.claude.com/docs/en/cli-reference).

```zsh
claude auth status --text
claude-work auth status --text
```

In Desktop, check the email in each window's account menu. If a short command is not recognized, open a new terminal or run `source "$HOME/.zshrc"`. If an alias or function with the same name already exists, check the existing definition and edit it.

*Verified as of 2026-10-07. Examples are for macOS and zsh.*
