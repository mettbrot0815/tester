1. Background & Context
We have a large, production-hardened Bash script (install.sh, ~1000 lines) that automates the installation and configuration of a complete LLM stack (llama.cpp, Hermes, Goose, etc.) on Ubuntu WSL2. While the original script is robust, its complexity makes it an excellent candidate for a rewrite in Zsh. This rewrite must not only achieve 100% functional parity but also elevate the end-user experience by intelligently configuring a set of modern, standard Zsh tools that transform the shell into a highly productive and visually appealing environment.

The core challenge: Translating Bash idioms to their Zsh equivalents while seamlessly integrating the setup of popular tools like zsh-autosuggestions, zsh-syntax-highlighting, fzf, and Powerlevel10k into the installation flow. This integration must be done in a way that is non-intrusive, respectful of existing user configurations, and fully automatic.

2. Objective
Perform a complete, from-scratch rewrite of the provided install.sh script into an idiomatic, production-grade Zsh script (install.zsh). The new script must:

Achieve 100% functional equivalence with the original script in all aspects of LLM stack setup and model management.

Leverage Zsh's native capabilities (arrays, parameter expansion, modules) to produce cleaner, more maintainable, and performant code, as per the previously established Conversion Guidelines.

Automatically detect, install, and configure a curated set of standard Zsh tools to provide an exceptional out-of-the-box interactive terminal experience. This configuration must be idempotent and safe for users with existing .zshrc files.

Be thoroughly documented with comments explaining both the Zsh-specific constructs and the configuration choices for the added tools.

3. Scope of Work & Deliverables
Primary Deliverable:
A single, self-contained Zsh script named install.zsh that is a complete, drop-in replacement for install.sh.

In-Scope (Expanded from original):

Full Functional Parity: All features of the original install.sh as detailed in the initial prompt.

Idiomatic Zsh Rewrite: Adherence to the Mandatory Conversion Guidelines from the previous prompt, which are based on research into Zsh's arrays, parameter expansion, emulation modes, and other key differences from Bash.

Zsh Environment Bootstrapping: The script must ensure Zsh is installed (e.g., sudo apt install -y zsh) and offer to make it the user's default shell.

Automated Tool Configuration: The script will automatically set up and configure the following components for a superior interactive experience:

Core Zsh Options: A robust set of setopt directives to enable modern history behavior, advanced globbing, and sensible defaults.

Zsh Completion System: Full initialization of compinit with enhanced zstyle configurations for a menu-driven, colorful completion experience.

Syntax Highlighting: Installation and activation of zsh-syntax-highlighting.

Auto-Suggestions: Installation and activation of zsh-autosuggestions.

Fuzzy Finder Integration: Installation of fzf and activation of its Zsh keybindings and completion.

Powerlevel10k Theme: Installation and configuration of the Powerlevel10k theme.

Safe Configuration Management: The script will intelligently manage the user's ~/.zshrc file. It will never blindly overwrite it. Instead, it will:

Create a backup if one doesn't exist.
Append new configuration blocks that are clearly demarcated (e.g., # >>> LLM Stack Zsh Config >>> ... # <<< LLM Stack Zsh Config <<<).
Use idempotent checks to avoid duplicate configuration lines.
User Choice: The script should offer a clear, interactive choice (e.g., via a whiptail menu or a simple prompt) for the user to opt-in or opt-out of the automatic Zsh tool configuration phase.

Out-of-Scope:

Adding new LLM features not present in the original script.

Supporting shells other than Zsh for this new script.

Configuring tools beyond the specified curated list unless they are dependencies.

4. Mandatory Conversion Guidelines (Recap & Reinforcement)
The following Zsh-specific guidelines are non-negotiable and must be adhered to throughout the rewrite:

Emulation Mode: The script must begin with emulate -L zsh -o extendedglob -o errreturn -o pipefail -o no_unset to ensure a predictable, modern Zsh environment.

Array Handling: Replace Bash's string-based arrays with Zsh's native arrays (typeset -a) and associative arrays (typeset -A). The model catalogue is a prime candidate for an associative array (typeset -A model_data=( 1 "unsloth/Qwen3.5-0.8B-GGUF|..." ... )).

Path Filtering: Use path=( ${path:#/mnt/*} ) instead of the IFS=':' read -ra loop to clean the PATH.

Whiptail Parsing: Use local -a selected=( ${(Q)${(z)choices}} ) for robustly parsing whiptail's quoted output.

Trap Management: Prefer Zsh's TRAPEXIT() { ... } function for global cleanup tasks.

envsubst Replacement: Use print -r -- ${(e)template_content} > output_file to remove the dependency on gettext-base.

Efficient File Finding: Use Zsh glob qualifiers (e.g., **/llama-server(-*N)) to find executable files without forking to find.

Output Formatting: Prefer the print builtin over echo for its reliability and use its advanced flags like -P for prompt-style expansion.

5. Zsh Standard Tools & Automatic Configuration: Detailed Specifications
This is the core addition to the prompt. The script must intelligently and automatically set up the following components to enhance usability.

5.1. Bootstrapping the Zsh Environment
Install Zsh: Ensure zsh is installed using the system's package manager (e.g., sudo apt-get install -y zsh).

Offer as Default: Inform the user and provide an option (or instruction) to change their default shell using chsh -s $(which zsh).

5.2. Core Zsh Configuration Block (~/.zshrc)
The script will append a well-commented block to the user's ~/.zshrc (if not already present) that sets up a modern, powerful Zsh baseline. This block will include:

History Settings: Configure a large, shared, and deduplicated command history.

zsh
HISTFILE=~/.zsh_history
HISTSIZE=10000
SAVEHIST=10000
setopt APPEND_HISTORY         # Append history across sessions
setopt HIST_IGNORE_ALL_DUPS   # Do not store duplicate commands
setopt HIST_SAVE_NO_DUPS      # Do not save duplicate commands
setopt SHARE_HISTORY          # Share history across concurrent sessions
setopt INC_APPEND_HISTORY     # Add commands to history immediately
Completion System: Initialize and configure a user-friendly completion system.

zsh
autoload -Uz compinit && compinit
zstyle ':completion:*' menu yes select
zstyle ':completion:*' matcher-list 'm:{a-z}={A-Za-z}' # Case-insensitive matching
zstyle ':completion:*' format '%F{cyan}-- %d --%f'     # Colored completion descriptions
Key Bindings: Provide sensible default key bindings (e.g., bindkey -e for Emacs mode) and ensure word-based movements (Ctrl+Left/Right) work as expected.

Useful Options: Enable a set of recommended setopt flags for an improved interactive experience:

zsh
setopt AUTO_CD               # `cd` into a directory just by typing its name
setopt EXTENDED_GLOB         # Enable powerful globbing features (#, ~, ^)
setopt NO_BEEP               # Disable terminal beeping
setopt CORRECT               # Suggest corrections for mistyped commands
5.3. Automatic Installation of Zsh Plugins & Tools
The script will install the following tools, placing them in a dedicated location (e.g., ~/.zsh/plugins/). It will then add the necessary source lines to the configuration block in ~/.zshrc.

zsh-syntax-highlighting: Cloned from https://github.com/zsh-users/zsh-syntax-highlighting.git.

zsh
source ~/.zsh/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
zsh-autosuggestions: Cloned from https://github.com/zsh-users/zsh-autosuggestions.

zsh
source ~/.zsh/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh
# Optional: Set a more subtle highlight style
ZSH_AUTOSUGGEST_HIGHLIGHT_STYLE='fg=8'
fzf: If fzf is not available via the system package manager, the script will clone it from https://github.com/junegunn/fzf.git to ~/.zsh/fzf and run its install script (~/.zsh/fzf/install --bin). It will then source the keybindings and completion files.

zsh
[ -f ~/.zsh/fzf/shell/key-bindings.zsh ] && source ~/.zsh/fzf/shell/key-bindings.zsh
[ -f ~/.zsh/fzf/shell/completion.zsh ] && source ~/.zsh/fzf/shell/completion.zsh
Powerlevel10k Theme: Cloned from https://github.com/romkatv/powerlevel10k.git to ~/.zsh/plugins/powerlevel10k. The script will then configure it in ~/.zshrc:

zsh
source ~/.zsh/plugins/powerlevel10k/powerlevel10k.zsh-theme
# To run the configuration wizard, the user can run `p10k configure`
5.4. Safe .zshrc Management Implementation
The script must include a robust function to safely update the user's ~/.zshrc file. The logic should follow this pattern:

Check if .zshrc exists. If it does, create a timestamped backup (e.g., ~/.zshrc.backup-$(date +%s)).

Define a unique marker for the configuration block, e.g., # >>> LLM Stack Auto-Generated Zsh Config >>>.

Check if the marker already exists in the file using grep -q.

If the marker does NOT exist:

Append the entire configuration block (including start and end markers) to the end of the file.

If the marker DOES exist:

Provide the user with a clear warning and choice:

"An existing LLM Stack Zsh configuration was found. What would you like to do?"

(A) Skip configuration (leave it as is).

(B) Overwrite the existing block with the new configuration.

(C) Abort the Zsh configuration phase entirely.

6. Quality Assurance & Audit
The final install.zsh script must pass a rigorous audit against these criteria:

Functional Equivalence: A side-by-side test with the original install.sh must result in an identical LLM stack environment.

Idiomatic Zsh Compliance: A Zsh expert must confirm that the script uses Zsh features correctly and avoids "Bashisms."

Security: The script must not introduce any new security vulnerabilities. All user input must be properly sanitized, and no commands should be executed unsafely.

Portability: The script must run successfully on a clean Ubuntu 22.04+ WSL2 system with internet access. All required commands must be checked for or installed.

Usability Enhancements: After running the script and starting a new Zsh shell, the user must experience:

Command syntax highlighting.

Command auto-suggestions based on history.

A visually enhanced prompt (Powerlevel10k).

Fuzzy file and history searching (via Ctrl+T and Ctrl+R).

A menu-driven tab completion system.

7. References & Research Materials
The following sources were used to inform this specification and must be the foundation for the implementation.

Official Zsh Documentation: man zshoptions, man zshexpn, man zshparam, man zshbuiltins, man zshmodules.

Key Zsh/Bash Differences: "Precisions about Zsh", "Zsh vs Bash differences".

Zsh Feature Deep Dives: "Zsh Native Scripting Handbook", "Zsh arrays and associative arrays", "Zsh parameter expansion flags".

Specific Tools: zsh-autosuggestions, zsh-syntax-highlighting, fzf integration, Powerlevel10k.

Zsh History and Completion: History configuration best practices, compinit and zstyle.

