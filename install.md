Install the AI That Works plugin on this computer for the user. Run each step yourself. Check that each step worked before you go to the next one. Tell the user in plain words what you did. Ask the user only when a step needs them: a click, a password, or an installer window.

<KEY> below is the licence key the user gave you in their message.

1. Run `git --version`. When it prints a version, go to step 2. Otherwise:
   - Run `xcode-select --install`. It opens a macOS window that installs git.
   - Tell the user to click Install in that window, and to reply once the install finishes.
   - When they reply, run `git --version` again.

2. Run `node --version`.
   - When it prints v22 or later, go to step 3.
   - When it prints an older version and `/usr/local/bin/node --version` prints v22 or later, the installer has already run but another Node comes first. Tell the user another Node is in the way, and stop.
   - When it prints an older version, run `dirname "$(dirname "$(command -v node)")"` to find the folder Node is installed in. When you can write to that folder, run `N_PREFIX=<that folder> npx -y n 22`, then run `node --version` again. When it now prints v22 or later, go to step 3.
   - When Node is missing, you can't write to that folder, or `node --version` still prints an older version:
     - Fetch https://nodejs.org/dist/latest-v22.x/SHASUMS256.txt with `curl -fsSL`, and read the `.pkg` file name from it.
     - Download that file from https://nodejs.org/dist/latest-v22.x/ into a temporary folder with `curl -fsSLO`.
     - Run `open` on the downloaded file.
     - Tell the user to click through the installer and type their computer password when it asks.
     - Tell the user that once the installer finishes, they need to quit this app completely, open it again, and paste this prompt again.
     - Stop here.

3. Run these two commands in order:
   - `npm config set //registry.useaithatworks.com/:_authToken <KEY>`
   - `npm config set allow-remote all`
   When the second command fails with "not a valid npm option", run `npm config get userconfig`. Add `allow-remote=all` as its own line at the end of the file it prints.

4. Install the plugin for each app whose command is on the PATH:
   - Claude Code (`claude`): run `claude plugin marketplace add zellwk/aithatworks-plugins`, then `claude plugin install atw@aithatworks`.
   - Codex (`codex`): run `codex plugin marketplace add zellwk/aithatworks-plugins`, then `codex plugin add atw@aithatworks`.
   When the marketplace is already added, go on to the install. When neither command is on the PATH, tell the user, and stop here. When an install fails with E401, tell the user their licence key didn't work and to reply to the email their key came in. Stop here.

5. Tell the user to start a new session and type "Set up AI That Works".
