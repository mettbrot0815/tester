# Project: LLM-Installer for Ubuntu WSL2

Short description: One‑script installer for llama.cpp, Hermes Agent, Goose, OpenCode, AutoAgent, OpenClaude, and a local WebUI on WSL2.
Tech stack: Bash (original) / Zsh (rewrite), Python (for HF tools), C++ (llama.cpp), Rust (Goose), Node.js (OpenClaude).

## My Preferences & Rules (YOU MUST FOLLOW THESE)

- Always think step-by-step before editing files.
- Be extremely careful with file edits — show me a diff or plan first when possible.
- Prefer small, safe changes over big refactors unless I explicitly ask for a full rewrite.
- Use clear, descriptive variable/function names.
- Add comments only when the code is complex or uses non‑obvious shell features (e.g., Zsh parameter expansion flags).
- **Never** commit secrets, tokens, `.env` files, or large binaries.
- Always run a syntax check after editing shell scripts (`bash -n` / `zsh -n`).

## Coding Style

- Follow existing style in the script (indentation with spaces, consistent quoting).
- Prefer `[[ ]]` over `[ ]` for tests.
- In Zsh:
  - Use `emulate -L zsh -o …` in functions.
  - Leverage arrays (`typeset -a`) and associative arrays (`typeset -A`) where appropriate.
  - Use `print -P` for colored output, not raw escape codes.
- Error handling: Explicit, never silent failures (`die` function).
- Keep functions focused and under ~50 lines when possible.

## How to Run the Project

- **Install script:** `bash install.sh` (original) or `zsh install.zsh` (rewrite)
- **Switch model only:** `SWITCH_MODEL_ONLY=1 bash install.sh`
- **Test syntax:** `bash -n install.sh` / `zsh -n install.zsh`
- **Lint (Bash):** `shellcheck install.sh`
- **Lint (Zsh):** `zsh -n install.zsh` and manual review (no official linter).

## Project Structure

~/
├── llm-models/ # Downloaded GGUF models
├── llama.cpp/ # Built llama.cpp source
├── hermes-agent/ # Hermes Agent installation
├── autoagent/ # AutoAgent installation
├── hermes-webui/ # Web UI for Hermes
├── start-llm.sh # Launch script (generated)
├── start-autoagent.sh # AutoAgent launcher (generated)
├── start-webui.sh # Web UI launcher (generated)
└── .config/ # Config files for Goose, OpenCode, etc.

## Important Notes

- This is designed for **Ubuntu WSL2**; hardware detection assumes `/proc` and `nvidia-smi`.
- The script runs a **local 9B model** on a consumer GPU — be patient with long downloads and builds.
- If you get stuck, ask for clarification instead of guessing.
- Prefer using tools (`read_file`, `edit_file`, `bash` commands) over assuming file contents.

## Memory / Context

Always keep in mind the user's goal: **Maintain and improve the LLM installer script, ensuring it works reliably on WSL2 and provides a smooth user experience.**

## Auto-Learning Rules (Very Important)

- After every significant task, successful change, or mistake correction, you MUST summarize what you learned in 2-4 bullet points.
- At the end of the session or when I say "update memory", propose improvements to this `agents.md` file.
- Use the `edit_file` tool to actually update `agents.md` with new rules, preferences, or lessons.
- Only add high-confidence learnings. Keep the file under 250 lines total.
- Structure new learnings like this:

**Learned [2026-04-10]:**
- Rule: When rewriting Bash scripts to Zsh, use emulate -L zsh -o extendedglob -o errreturn -o pipefail -o no_unset to ensure predictable Zsh environment.
- Example: In install.zsh, started with emulate -L zsh ... and used path=( ${path:#/mnt/*} ) instead of IFS loop.
- Why: Ensures idiomatic Zsh code and avoids Bashisms for better performance and maintainability.

**Learned [2026-04-10]:**
- Rule: Use typeset -A for associative arrays in Zsh instead of Bash arrays for structured data like model catalogues.
- Example: typeset -A MODELS; MODELS=( 1 "data..." ) and accessed with $MODELS[$idx]
- Why: Zsh associative arrays are more powerful and allow key-based access without indexing gymnastics.

**Learned [2026-04-10]:**
- Rule: Replace envsubst with print -r ${(e)template_content} for safe variable expansion in Zsh.
- Example: template_content=$(<file); print -r ${(e)template_content} > output
- Why: Avoids dependency on gettext-base and uses native Zsh parameter expansion for security.

**Learned [2026-04-10]:**
- Rule: Use print -P for colored output in Zsh instead of echo with raw escapes.
- Example: print -P "${CYN}[*] message${RST}"
- Why: Reliable formatting and leverages Zsh's prompt expansion for better color handling.

**Learned [2026-04-10]:**
- Rule: For whiptail output parsing, use local -a selected=( ${(Q)${(z)choices}} ) to robustly split quoted strings.
- Example: In select_optional_components, parsed choices with Zsh array expansion.
- Why: Handles quoted output safely without IFS manipulation, reducing injection risks.

**Learned [2026-04-10]:**
- Rule: Use Zsh glob qualifiers like **/file(-*N) for efficient file finding without forking find.
- Example: found=( ${HOME}/llama.cpp/**/llama-server(-*N) )[1] in find_llama_server.
- Why: Leverages Zsh's built-in globbing for faster, more reliable file searches.

**Learned [2026-04-10]:**
- Rule: Always install Zsh tools (zsh-syntax-highlighting, autosuggestions, fzf, Powerlevel10k) and configure .zshrc safely with markers.
- Example: Cloned tools to ~/.zsh/plugins/, added config block with # >>> ... <<< markers, checked for existing before appending.
- Why: Provides modern, productive shell experience and respects user configurations.

**Learned [2026-04-10]:**
- Rule: Adapt .bashrc helpers to .zshrc for Zsh, using Zsh-specific features like [[ -o interactive ]] and print -P.
- Example: Converted functions to use Zsh syntax, added Zsh aliases and auto-start logic.
- Why: Ensures seamless integration and leverages Zsh strengths for better user experience.
