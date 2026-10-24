# Docker & GitHub Actions Learning Vault

Learn Docker and GitHub Actions through step-by-step tutorials, practical examples, exercises, and knowledge checks. These notes form an **Obsidian vault**: a folder of Markdown files that Obsidian opens as a connected collection of lessons.

This guide explains how to download the tutorials, open them in Obsidian, and follow the learning path on **Windows, macOS, or Linux**.

## What you can learn

- **Docker:** containers, images, Dockerfiles, Compose, storage, networking, command flags, and Engine configuration.
- **GitHub Actions:** workflows, events, jobs, runners, variables, secrets, and troubleshooting.
- **Docker with CI/CD:** automated builds, application checks, image publishing, approvals, and deployment examples.
- **Further practice:** Dockerfile and Compose mastery, worked exercises, command references, and a glossary.

Some additional topics are marked **(planned)** in the question lists. Those labels identify future lessons.

## 1. Install Obsidian

Download and install the application for your operating system from the [official Obsidian download page](https://obsidian.md/download).

You only need Obsidian to read and navigate the vault. Docker, Git, and a GitHub account are needed only for exercises or download methods that explicitly require them. No community plugins are required.

## 2. Download the tutorials

### Option A: Download ZIP

Use this option if you are new to Git.

1. Open the [Docker & GitHub Actions repository](https://github.com/git-shad/Docker---Github-Action).
2. Above the file list, select **Code**, then **Download ZIP**.
3. Extract the ZIP using your operating system's archive tool. On Windows, you can right-click the ZIP and choose **Extract All**.
4. Move the extracted folder somewhere you want to keep your learning notes, such as your Documents folder.
5. Find the folder that directly contains **`Home.md`** and **`00 - Learning Roadmap.md`**. This is the folder you will open in Obsidian.

The extracted folder may be named `Docker---Github-Action-main`. Its contents should include:

```text
Docker---Github-Action-main/
  README.md
  Home.md
  00 - Learning Roadmap.md
  01 - Docker Fundamentals/
  03 - GitHub Actions Fundamentals/
  Docker/
  GitHub Actions/
  ...
```

This is a partial folder listing. Keep all the downloaded lesson folders together so links between lessons work.

GitHub documents this process in [Downloading source code archives](https://docs.github.com/en/repositories/working-with-files/using-files/downloading-source-code-archives).

### Option B: Clone with Git

If Git is already installed, open a terminal in the parent folder where you want to store the vault and run:

```bash
git clone https://github.com/git-shad/Docker---Github-Action.git
```

This command works in Bash on Linux or macOS, Git Bash on Windows, and Windows PowerShell when Git is available. It creates a folder named `Docker---Github-Action`; select that folder in the next step. See GitHub's [cloning guide](https://docs.github.com/en/repositories/creating-and-managing-repositories/cloning-a-repository) for details.

## 3. Open the folder in Obsidian

1. Launch Obsidian.
2. On the vault selection screen, find **Open folder as vault** and select **Open**.
3. Select the extracted or cloned folder that directly contains `Home.md`.
4. Confirm **Open** in the folder picker.
5. In Obsidian's file explorer, open **`Home.md`**.

If another vault is already open, use the vault menu at the bottom of the left sidebar and select **Manage Vaults…**. You can also open the Command palette with `Ctrl+P` on Windows/Linux or `Cmd+P` on macOS and run **Open another vault**. Then select **Open folder as vault**.

Use the existing downloaded folder for this step. Selecting only `Docker/` or `GitHub Actions/` leaves the other lessons outside the vault and prevents some links from working.

These steps follow Obsidian's [vault management guide](https://obsidian.md/help/manage-vaults) and [Command palette guide](https://obsidian.md/help/plugins/command-palette).

**Expected result:** `Home.md` opens with links to both subjects, their question lists, the complete roadmap, and example collections.

## 4. Start learning

1. From `Home.md`, open **the complete learning roadmap and checklist**.
2. Start with **Introduction to Docker** in **01 - Docker Fundamentals**, then follow the lessons in order. If you already know Docker, use the GitHub Actions learning path and check each lesson's prerequisites.
3. Read a lesson's explanation and prerequisites before trying its walkthrough.
4. Complete the practice task, answer the knowledge questions, and expand the answers to check your understanding.
5. Use the **Previous Lesson** and **Next Lesson** links at the bottom of a tutorial to move through the sequence.
6. Return to `00 - Learning Roadmap.md` and tick the checkbox for a lesson you have completed. Your progress is stored in your local copy of that file.

You can also browse by subject or question:

- [[Home|Vault home]]
- [[00 - Learning Roadmap|Complete learning roadmap and checklist]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker learner questions]]
- [[GitHub Actions/GitHub Actions Index|GitHub Actions learning path]]
- [[GitHub Actions/GitHub Actions Questions|GitHub Actions learner questions]]
- [[Docker/Examples/Docker Examples|Docker examples]]
- [[GitHub Actions/Examples/GitHub Actions Examples|GitHub Actions examples]]
- [[08 - Dockerfile and Compose Mastery/00 - Mastery Roadmap|Dockerfile and Compose mastery roadmap]]

**Reading this on GitHub?** The links above use Obsidian's `[[note]]` syntax. Open the downloaded vault in Obsidian to follow them. On GitHub, you can browse the same `.md` files using the repository's file list. Obsidian explains the syntax in its [internal links guide](https://obsidian.md/help/links).

## Useful Obsidian controls

| Task | Windows / Linux | macOS |
| --- | --- | --- |
| Find and open a note by name | `Ctrl+O` | `Cmd+O` |
| Search across the vault | `Ctrl+Shift+F` | `Cmd+Shift+F` |
| Switch between editing and Reading view | `Ctrl+E` | `Cmd+E` |
| Open the Command palette | `Ctrl+P` | `Cmd+P` |

For example, open the Quick switcher, type `Home`, and select the existing Home note. Search for `volumes`, `secrets`, or a question you want to answer to find relevant lessons.

Use **Reading view** for formatted lessons and clickable links. Switch to editing when you want to add notes. In Reading view, click a roadmap checkbox to record progress; in Source mode, change `- [ ]` to `- [x]`.

These are the documented default controls for [Quick switcher](https://obsidian.md/help/plugins/quick-switcher), [Search](https://obsidian.md/help/plugins/search), and [reading and editing views](https://obsidian.md/help/edit-and-read). Custom hotkeys may differ.

## Before running tutorial examples

The examples are documented learning exercises; they were not executed while writing the vault. Expected results describe what to look for, rather than recorded successful runs.

Most shell examples target **Ubuntu Linux and Bash**, with platform differences explained in the lessons. Windows PowerShell and macOS may need different commands or setup. Read each lesson's execution context, prerequisites, and cleanup instructions, especially for commands that remove data, publish images, or change a server.

Configuration examples live inside Markdown code blocks. When a lesson asks you to create a Dockerfile, Compose file, or workflow, create it in the practice project or repository specified by that lesson. Some application exercises require an existing application; this vault contains the teaching notes.

After downloading, you can read the local notes offline. External references and exercises that download images, packages, or use GitHub require internet access.

## Keep your progress when updating

A ZIP download is a snapshot and does not update automatically.

1. Back up your current vault, including your checked roadmap and personal notes.
2. Download and extract the newer ZIP into a separate folder.
3. Open the newer folder as a vault and transfer your progress and personal notes after reviewing any lesson changes.

If you cloned with Git, review and preserve local edits before updating through your usual Git workflow. Checking roadmap boxes and changing Obsidian settings can modify tracked files.

## Troubleshooting

| Problem | What to check |
| --- | --- |
| The vault looks empty or `Home.md` is missing | Extract the ZIP first, then select the inner folder that directly contains `Home.md`. |
| Obsidian opens an old note instead of the home page | Use the file explorer or Quick switcher to open the existing `Home` note. |
| A link points to a missing note | Check that you downloaded the whole repository and opened its root folder. Keep the original folder names and layout. |
| You see brackets, code fences, or diagram source | Switch to Reading view with `Ctrl+E` or `Cmd+E`. |
| A keyboard shortcut does nothing | Check **Settings → Core plugins** for Quick switcher, Search, or Command palette, and check **Settings → Hotkeys** for customized shortcuts. |
| Your progress is absent after downloading an update | Checkboxes belong to your previous local roadmap. Recover them from your backup and transfer them to the newer copy. |

Setup instructions were checked against the linked official Obsidian and GitHub documentation on **2026-10-24**. Opening the vault in the desktop application was not tested as part of writing this README.
