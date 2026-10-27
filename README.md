# goodnotes-open

A [Hermes Agent](https://hermes-agent.nousresearch.com) skill that opens GoodNotes 5/6 notebook files (`.goodnotes`) — from your local disk **or** a connected cloud service (Google Drive, Dropbox, OneDrive, iCloud, Goodnotes Cloud) — inspects their contents, and converts them to readable formats (PDF, SVG, JSON) without the GoodNotes app.

`.goodnotes` is a proprietary, undocumented container format. This skill teaches the agent how to work with it using open-source, cross-platform tools.

## Features

- **Locate anywhere** — finds `.goodnotes` files on the local filesystem or in any connected cloud service (Google Drive, Dropbox, OneDrive, iCloud, Goodnotes Cloud).
- **Verify & inspect** — confirms a file is a valid GoodNotes container and lists its pages, strokes, and embedded attachments (images, audio, PDFs).
- **Convert** — exports notebooks to multi-page PDF, vector SVG, or structured JSON.
- **Extract media** — pulls out embedded images, audio, and PDFs.
- **No GoodNotes app required** — works on Linux, macOS, and Windows.

## How it works

A `.goodnotes` file is a ZIP container holding:

- `schema.pb` — the schema version (24 or 25)
- `index.*.pb` — Protobuf-encoded page/document indexes
- `notes/<UUID>` — page files with strokes (Troy Hanson TPL, LZ4/`bv41`-compressed)
- `attachments/<UUID>` — embedded images, audio, and PDFs (no file extensions)

The skill verifies the container, inspects its members, then hands off to a conversion tool to produce readable output.

## Installation

### 1. Install the skill

```bash
hermes skills install <your-github-user>/goodnotes-open
```

Or copy the `goodnotes-open/` directory into your skills folder:

```bash
cp -r goodnotes-open ~/.hermes/skills/productivity/
```

### 2. Install a conversion tool (pick one)

**For PDF output (simplest):**

```bash
pip install pymupdf
# then download goodnotes2pdf.py from https://github.com/skoblens5/goodnotes2pdf
```

**For PDF / SVG / JSON (deepest inspection):**

```bash
git clone https://github.com/Kaih1825/parser-for-goodnotes
cd parser-for-goodnotes && pip install -e .
```

### 3. Connect cloud storage (optional)

If your notebooks live in Google Drive, Dropbox, OneDrive, or iCloud, connect the account in Hermes (`manage_connections`). The skill downloads a local copy before converting.

## Usage

Ask Hermes in plain language:

> "Open my notes.goodnotes and show me what's inside."
> "Convert my math notebook to PDF."
> "Find my GoodNotes file in Google Drive and export it as SVG."

The agent will:

1. Locate the file (local or cloud).
2. Verify it's a valid `.goodnotes` container.
3. Inspect its contents.
4. Convert to the requested format.
5. Deliver the output file.

### Manual inspection

```bash
python3 scripts/inspect_goodnotes.py notes.goodnotes
```

## Cloud storage notes

- **Auto-backup** (Google Drive / Dropbox / OneDrive): stores `.goodnotes` copies — these are directly usable.
- **iCloud sync / Goodnotes Cloud**: store the library differently. If a file isn't where you expect, tell the agent which sync method you use.

## Limitations

- `.goodnotes` is proprietary and reverse-engineered; a future GoodNotes update can change the format.
- There is **no reliable open-source writer** — you can read and convert, but not faithfully edit and re-save a `.goodnotes` file. For editing, use the GoodNotes app.
- Notability (`.note`) and reMarkable (`.rm`) files are different formats and not covered.

## License

MIT — see [LICENSE](LICENSE).

## Contributing

PRs welcome. Keep the skill universal and cross-platform; no machine-local paths or credentials in the skill content.
