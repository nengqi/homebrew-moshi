# homebrew-moshi

Homebrew tap for [Moshi](https://getmoshi.app).

This installs the `moshi` CLI and the `moshi-hook` service package for macOS and Linuxbrew.

## Install

```bash
brew tap rjyo/moshi
brew install moshi-hook
brew services start moshi-hook
```

Release tarballs are served from `cdn.getmoshi.app` — no GitHub token needed for the binary download.

## Setup

Pair this host from the Moshi app, then install supported agent hooks:

```bash
moshi host setup
moshi install
```

`brew services` keeps the daemon alive across reboots and restarts it on crash. Logs go to `$(brew --prefix)/var/log/moshi-hook.log`.

To run it in the foreground instead (useful while debugging):

```bash
brew services stop moshi-hook
moshi serve -v
```

## Common commands

```bash
moshi .              # open or attach to a tmux session for this directory
moshi diff .         # open the local diff viewer
moshi status
moshi logs -f
moshi version
```

## Upgrade

```bash
brew update
brew upgrade moshi-hook
brew services restart moshi-hook
```

Homebrew owns the installed binary, so use `brew upgrade moshi-hook` instead of `moshi update`.

## Running behind a proxy

`moshi-hook` reads `HTTPS_PROXY` / `HTTP_PROXY` from its environment; there is no `moshi set` key for a proxy. To route the service through one on macOS, add the variables to the service plist and reload it:

```bash
PLIST=~/Library/LaunchAgents/sh.brew.moshi-hook.plist
/usr/libexec/PlistBuddy \
  -c 'Add :EnvironmentVariables dict' \
  -c 'Add :EnvironmentVariables:HTTPS_PROXY string http://127.0.0.1:7890' \
  -c 'Add :EnvironmentVariables:HTTP_PROXY string http://127.0.0.1:7890' \
  "$PLIST"
launchctl unload "$PLIST" && launchctl load -w "$PLIST"
```

`brew services start` / `restart` and `brew upgrade` regenerate this plist from the formula and drop the variables without warning, so re-apply them afterwards (or reload with `launchctl` instead of `brew services restart`).

## Uninstall

```bash
brew services stop moshi-hook
moshi uninstall
moshi unpair
brew uninstall moshi-hook
brew untap rjyo/moshi
```

## Reporting issues

Email <support@getmoshi.app> or DM the team — this tap repo doesn't track issues.

`Formula/moshi-hook.rb` is auto-published on every release; manual edits will be overwritten.
