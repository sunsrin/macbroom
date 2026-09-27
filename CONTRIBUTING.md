# Contributing to macbroom 🧹

Thank you for your interest in improving **macbroom**! We welcome contributions, whether it's adding a new target, fixing a bug, or improving documentation.

---

## Guiding Principles

When proposing any changes or new cleanup targets, please adhere to these non-negotiable safety rules:

1. **Safety First**:
   - **Never** add cleanups that require `sudo` or modify system-level directories outside of user space (`$HOME`).
   - **Never** touch persistent user data, browsing history, saved passwords, authentication tokens, session cookies, bookmarks, or SSH/GPG keys.
2. **Opt-in for Aggressive Caches**:
   - Only universally safe, self-regenerating caches belong in the default interactive pass.
   - Any cache that requires re-downloading large dependencies (e.g. Playwright browsers, Docker images, Xcode DerivedData, AI model weights) or affects offline PWAs must be placed behind an explicit opt-in flag.
3. **Inspectable & Dry-Run Capable**:
   - Every single cleanup target must support `--dry-run` and report accurate human-readable sizes before deletion.
4. **Process Awareness**:
   - If a target directory belongs to an active desktop application (e.g., Slack, Chrome, Xcode), use `check_app_running` to verify the application is closed before attempting deletion.
5. **No External Dependencies**:
   - Keep the script written in native `zsh` relying only on standard macOS system utilities. Avoid requiring Python, Node, or third-party CLI packages to run the base script.

---

## How to Add a New Cleanup Target

1. **Define the Flag**:
   - Add a variable at the top of [`macbroom`](macbroom) (e.g., `CLEAR_MYAPP=0`).
   - Add the flag to the argument parsing `case` statement and to the appropriate bundle flags (e.g., `--all-dev` or `--all-apps`).
   - Document the flag in `show_help()` and in `README.md`.

2. **Implement the Section**:
   - Use the `remove_dir` helper for directories:
     ```zsh
     if [[ "$CLEAR_MYAPP" -eq 1 ]]; then
       if check_app_running "MyApp"; then
         remove_dir "MyApp cache" "$HOME_DIR/Library/Caches/com.example.myapp"
       fi
     else
       echo "[info] MyApp cache is NOT touched by default. Use --myapp to clean."
     fi
     ```
   - For official CLI tool cleanups (like `npm cache clean --force` or `docker system prune`), check for the binary first using `command -v`.

3. **Test with Dry-Run**:
   - Validate your changes with syntax checks and dry runs:
     ```bash
     zsh -n macbroom
     ./macbroom --dry-run --full
     ```

---

## Code Style

- Use `[[ ... ]]` for test conditions in `zsh`.
- Always quote paths: `"$target_dir"`.
- Use the zsh null-glob qualifier `(N)` when globbing (e.g., `"$DIR"/*(N)`).
- **Important**: Do not use `path` as a variable name in functions (in `zsh`, `path` is tied to `$PATH` and will overwrite your environment binary lookup paths). Use `target_dir` or `item_path`.

---

## Submitting Pull Requests

1. Fork the repository.
2. Create a feature branch: `git checkout -b feature/add-new-cache-target`.
3. Commit your changes with clear, descriptive commit messages.
4. Push to your branch and open a Pull Request.
