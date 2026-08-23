# My Notes

A personal knowledge base of study notes, written in Markdown and organised by subject. Every folder holds one or more notes files plus a shared `images` folder of diagrams.

Built for [Obsidian](https://obsidian.md/), so links, tags and the graph view all resolve correctly when the repo is opened as a vault.

---

## Getting Started

**1. Clone the repo**

```bash
git clone https://github.com/your-username/your-notes-repo.git
```

**2. Install Obsidian**

It is free and available for Windows, macOS, Linux, iOS and Android at [obsidian.md](https://obsidian.md/).

**3. Open the folder as a vault**

Launch Obsidian, click **Open folder as vault**, and select the folder you just cloned.

> Open the whole folder as a vault rather than double clicking an individual `.md` file. Obsidian needs the vault root to resolve internal links and build the graph.

---

## Structure

```text
.
├── general-notes/                 Fundementals about github, file systems, and shortcuts
├── python/                        Python language fundamentals
├── data-science/                  Analytics, machine learning and data engineering
├── databases/                     Data modelling and SQL
├── java/                          Java language fundementals
├── front-end-development/         HTML, CSS, JavaScript, TypeScript, React and Next
├── back-end-development/          Spring Boot
├── linux-bash-and-computers/      Computer systems, networks and the shell
├── linux-server-management/       Managing a linux server
├── history/                       History notes
└── graphs/                        A structure folder used to manage obsidian graphs
```

Every folder follows the same pattern.

```text
folder-name
├── Topic_One.md
├── Topic_Two.md
└── images
    ├── diagram-one.svg
    └── diagram-two.svg
```

---

## Conventions

The files are all written the same way, so they read consistently no matter which one you open.

- `#` for the title, `##` for sections, separated by `---` rules.
- `###` subtitles only inside a section, and always with prose between the `##` and the first `###`.
- Fenced code blocks with a language tag on every one.
- Diagrams are hand built **SVG**, stored in the folder's own `images` directory and linked relatively, so nothing depends on an external host.
- Most files close with a **Quick Recap** section, which is the part to reread before an exam.

---

## Reading Without Obsidian

The files are plain Markdown, so any editor or the GitHub web view will render them fine, including the SVG diagrams and the code blocks.

What you lose outside Obsidian is backlinks, tags and the graph view.
