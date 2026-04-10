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

**Learned [Date]:**
- Rule: ...
- Example: ...
- Why: ...
