# macbroom 🧹

A transparent, safe, zero-dependency storage cleanup tool for macOS tailored for developers and power users.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Platform: macOS](https://img.shields.io/badge/Platform-macOS-lightgrey.svg)]()
[![Shell: zsh](https://img.shields.io/badge/Shell-zsh-brightgreen.svg)]()

---

## Why macbroom?

Most storage cleaners for macOS fall into two categories:
1. **Commercial & opaque apps**: Proprietary tools that run blackbox background daemons and charge recurring subscriptions.
2. **Aggressive scripts**: Generic cleanup scripts that require `sudo`, blindly wipe databases, or inadvertently clear active browser sessions, saved passwords, and essential developer state.

**macbroom** is built on different principles:
- **No `sudo` Required**: Operates strictly within user-space permissions (`$HOME`).
- **No Data Loss**: Never touches saved passwords, browser profiles, bookmarks, cookies, or history.
- **Inspectable Before Execution**: Full `--dry-run` mode shows path and size before a single byte is touched.
- **Process-Aware**: Detects running applications (Chrome, Edge, Android Studio, Slack, Zoom, Xcode) to avoid file lock conflicts or database corruption.
- **Developer-First**: Reclaims gigabytes from package managers, build artifacts, container caches, and AI model weights.
- **Zero External Dependencies**: Built in pure `zsh` using macOS native utilities.

---

## Quick Start

### 1. Clone & Set Permissions

```bash
git clone https://github.com/sunsrin/macbroom.git
cd macbroom
chmod +x macbroom
```

### 2. Preview First (Recommended)

Always start with a dry run to inspect what would be cleaned without modifying anything:

```bash
./macbroom --dry-run
```

### 3. Run Interactive Cleanup

Prompts you with `[y/N]` before each individual directory or tool cache:

```bash
./macbroom
```

### 4. Automated Cleanup (Non-Interactive)

Run recommended safe cleanups without interactive prompts:

```bash
./macbroom --yes
```

---

## Usage & Flag Reference

```bash
./macbroom [modes] [options]
```

### Modes

| Flag | Description |
| :--- | :--- |
| *(default)* | **Interactive mode**: Displays sizes and prompts `[y/N]` before removing each cache. |
| `--dry-run` | **Preview mode**: Calculates and prints reclaimable sizes without deleting anything. |
| `--yes` | **Unattended mode**: Runs cleanups automatically without prompts. |

### Bundle Flags

| Flag | Description |
| :--- | :--- |
| `--all-dev` | Enables all developer targets (`xcode`, `android-studio`, `npm`, `gradle`, `bun`, `cargo`, `playwright`, `docker`). |
| `--all-browsers` | Enables aggressive browser targets (Service Worker CacheStorage and Chrome On-Device AI models). |
| `--all-apps` | Enables cache cleanup for desktop apps (Slack, Discord, Zoom, Spotify). |
| `--full` | Enables all opt-in targets together. |

### Individual Opt-in Targets

#### Developer & Build Tools
| Flag | Target | Description |
| :--- | :--- | :--- |
| `--xcode` | `~/Library/Developer/Xcode/DerivedData`, `Archives`, `DeviceSupport` | Reclaims massive build artifacts and old iOS simulator caches. |
| `--android-studio` | `~/Library/Caches/Google/AndroidStudio*` | Cleans versioned Android Studio build & system caches. |
| `--npm-cache` | `~/.npm/_cacache` | Runs `npm cache clean --force`. |
| `--gradle-cache` | `~/.gradle/caches` | Removes downloaded Gradle artifacts and wrapper caches. |
| `--bun-cache` | `~/.bun/install/cache` | Purges Bun package manager cache. |
| `--cargo-cache` | `~/.cargo/registry/cache` | Removes Rust/Cargo package registry archives. |
| `--playwright` | Playwright browsers | Runs `npx playwright uninstall --all`. |
| `--docker` | Docker daemon | Runs `docker system prune -f` if Docker is running. |

#### Web Browsers & Electron Apps
| Flag | Target | Description |
| :--- | :--- | :--- |
| `--chrome-sw-cache` | `Service Worker/CacheStorage` (All profiles) | Removes cached offline web app assets across `Default`, `Profile 1`, etc. |
| `--chrome-ai-model` | `Chrome/OptGuideOnDeviceModel` | Removes Chrome's local generative AI model directory (~4 GB). |
| `--apps-cache` | Slack, Discord, Zoom, Spotify | Cleans temporary HTTP and media caches for Electron/desktop clients. |

#### Machine Learning & AI
| Flag | Target | Description |
| :--- | :--- | :--- |
| `--ai-cache` | `~/.cache/huggingface/hub`, `~/.cache/torch/hub` | Removes downloaded weights and model checkpoints. |

#### System Maintenance
| Flag | Target | Description |
| :--- | :--- | :--- |
| `--logs` | `~/Library/Logs/DiagnosticReports` | Removes application crash and diagnostic dumps. |
| `--trash` | `~/.Trash` | Empties the user's Trash folder. |

---

## Safety Guarantees

| Category | Action | Why |
| :--- | :--- | :--- |
| **Passwords & Keychain** | **NEVER TOUCHED** | Stored in secure macOS keychain and encrypted profile stores. |
| **Browser History & Cookies** | **NEVER TOUCHED** | You will not be logged out of your active browser sessions. |
| **Git Repositories & SSH Keys** | **NEVER TOUCHED** | No workspace files or `~/.ssh` keys are ever altered. |
| **Running Applications** | **CHECKED** | Prompts or skips when an application is open to prevent locked file errors. |
| **Root & System Files** | **NEVER TOUCHED** | The script refuses to run with `sudo` and validates every path against root/home roots. |

---

## Examples

#### 1. Clean default caches and developer tools:
```bash
./macbroom --all-dev
```

#### 2. Full disk audit preview (see all reclaimable space):
```bash
./macbroom --dry-run --full
```

#### 3. Unattended clean with specific targets:
```bash
./macbroom --yes --xcode --npm-cache --gradle-cache --trash
```

---

## Contributing

Contributions are welcome! Please check out [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on proposing new cleanup targets or improvements.

---

## License

This project is licensed under the [MIT License](LICENSE).
