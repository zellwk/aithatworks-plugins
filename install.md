Install the AI That Works plugin on this computer for the user. Run each step yourself. Check that each step worked before you go to the next one. Tell the user in plain words what you did. Ask the user only when a step needs them: a click, a password, or an installer window.

<KEY> below is the licence key the user gave you in their message.

Find out first whether this computer runs macOS or Windows. In each step, follow only the lines for that system. On Windows, run every command in PowerShell, and:

- Before your first `winget` command, run `winget --version`. When it fails, run `Start-Process "ms-windows-store://pdp/?productid=9NBLGGH4NNS1"`, ask the user to click Get or Update on App Installer, and wait until they say it has finished.
- When a command makes Windows ask "Do you want to allow this app to make changes to your device?", ask the user to click Yes, and wait until they say it's done.
- After a `winget install` or `winget upgrade`, refresh the PATH in the same command as your next check, by putting this line in front of it: `$env:Path = [Environment]::GetEnvironmentVariable('Path','Machine') + ';' + [Environment]::GetEnvironmentVariable('Path','User')`. When the check still can't find what winget installed, tell the user to quit this app completely, open it again, and paste this prompt again. Stop here.
- When PowerShell refuses to run `npm` because running scripts is disabled, run `npm.cmd` in its place.

1. Run `git --version`. When it prints a version, go to step 2. Otherwise:
   - macOS:
     - Run `xcode-select --install`. It opens a macOS window that installs git.
     - Tell the user to click Install in that window, and to reply once the install finishes.
     - When they reply, run `git --version` again.
   - Windows: run `winget install -e --id Git.Git --accept-source-agreements --accept-package-agreements`. Then refresh the PATH and run `git --version` again.

2. Run `node --version`. When it prints v22 or later, go to step 3. Otherwise:
   - macOS:
     - When it prints an older version and `/usr/local/bin/node --version` prints v22 or later, the installer has already run but another Node comes first. Tell the user another Node is in the way, and stop.
     - When it prints an older version, run `dirname "$(dirname "$(command -v node)")"` to find the folder Node is installed in. When you can write to that folder, run `N_PREFIX=<that folder> npx -y n 22`, then run `node --version` again. When it now prints v22 or later, go to step 3.
     - When Node is missing, you can't write to that folder, or `node --version` still prints an older version:
       - Fetch https://nodejs.org/dist/latest-v22.x/SHASUMS256.txt with `curl -fsSL`, and read the `.pkg` file name from it.
       - Download that file from https://nodejs.org/dist/latest-v22.x/ into a temporary folder with `curl -fsSLO`.
       - Run `open` on the downloaded file.
       - Tell the user to click through the installer and type their computer password when it asks.
       - Tell the user that once the installer finishes, they need to quit this app completely, open it again, and paste this prompt again.
       - Stop here.
   - Windows:
     - When Node is missing, run `winget install -e --id OpenJS.NodeJS.LTS --accept-source-agreements --accept-package-agreements`.
     - When it prints an older version, run `winget upgrade -e --id OpenJS.NodeJS.LTS --accept-source-agreements --accept-package-agreements`.
     - Then refresh the PATH and run `node --version` again. When it still prints an older version, tell the user another Node is in the way, and stop.

3. Run these two commands in order. Every member's setup includes `allow-remote all`, so set it without asking the user.
   - `npm config set //registry.useaithatworks.com/:_authToken <KEY>`
   - `npm config set allow-remote all`
   When the second command fails with "not a valid npm option", run `npm config get userconfig`, then:
   - macOS: Add `allow-remote=all` as its own line at the end of the file it prints.
   - Windows: Append the line as ASCII with `Add-Content -Encoding ascii -Path "<that file>" -Value ([Environment]::NewLine + 'allow-remote=all')`. Use this command in place of `>` or `>>`, which write UTF-16 in Windows PowerShell, and npm ignores a UTF-16 `.npmrc`.

4. When neither `claude` nor `codex` is on the PATH:
   - macOS: tell the user, and stop here.
   - Windows: install the Claude Code command with `winget install -e --id Anthropic.ClaudeCode --accept-source-agreements --accept-package-agreements`. The Code tab in the Claude desktop app doesn't add it. Then refresh the PATH and run `claude --version`.

   Install the plugin for each app whose command is on the PATH:
   - Claude Code (`claude`): run `claude plugin marketplace add zellwk/aithatworks-plugins`, then `claude plugin install atw@aithatworks`.
   - Codex (`codex`): run `codex plugin marketplace add zellwk/aithatworks-plugins`, then `codex plugin add atw@aithatworks`.
   When the marketplace is already added, go on to the install. When an install fails with E401, tell the user their licence key didn't work and to reply to the email their key came in. Stop here.

5. Tell the user to start a new session and type "Set up AI That Works".
