# Hosting Custom Samples for Strudel (PsyTech)

This guide explains the **easiest, fastest, and most robust way** to make your audio samples available for use in [Strudel](https://strudel.cc).

The recommended approach uses:
- A public GitHub repository (`psyTech-public`)
- The official `@strudel/sampler` tool to programmatically generate the required `strudel.json` mapping
- Strudel's built-in `github:` shortcut for extremely short, clean loading syntax

This is the same pattern used by popular community sample packs (Dirt-Samples, breakbeat collections, etc.).

## Why This Method?

| Benefit                    | Description |
|---------------------------|-------------|
| **Programmatic**          | One command generates the entire sample map |
| **Shortest URLs**         | Load everything with `samples('github:yourusername/psyTech-public')` |
| **Reliable & Permanent**  | GitHub hosting + version control |
| **Community Standard**    | Works exactly like existing popular packs |
| **Easy to Update**        | Push new samples → they become available immediately |
| **Shareable**             | One line for anyone to load your full library |

---

## Recommended Repository Structure

For the `psyTech-public` repo, use this layout:

```
psyTech-public/
├── strudel.json          ← Generated mapping (at root)
└── wav/                  ← All your samples live here
    ├── bd/               ← Bass drums / kicks
    │   └── kick-1.wav
    ├── sd/               ← Snares
    │   └── snare-1.wav
    ├── hh/               ← Hi-hats
    │   └── closed-1.wav
    ├── breaks/
    │   └── amen.wav
    └── atmos/
        └── pad-1.wav
```

> **Note**: The `wav/` folder keeps your raw audio organized. The generated `strudel.json` will reference files inside it.

---

## Step-by-Step Setup

### 1. Prepare Your Local Samples

Organize your audio files (`.wav`, `.mp3`, or `.ogg`) into category subfolders inside a `wav/` directory.

Example:
```bash
mkdir -p wav/{bd,sd,hh,breaks,atmos}
# Copy or move your samples into the appropriate folders
```

### 2. Generate `strudel.json` Programmatically

Run the official Strudel sampler tool from **inside** the `wav/` folder and output the JSON to the repo root:

```bash
cd wav
npx --yes @strudel/sampler --json > ../strudel.json
```

This automatically creates a `strudel.json` file at the root of your repo with the correct sample mappings based on your folder structure.

### 3. Add `_base` to `strudel.json` (Important for `wav/` structure)

Open the generated `strudel.json` and add a `_base` key at the top so Strudel knows where to find the audio files:

```json
{
  "_base": "https://raw.githubusercontent.com/yourusername/psyTech-public/main/wav/",
  "bd": "bd/kick-1.wav",
  "sd": "sd/snare-1.wav",
  "hh": "hh/closed-1.wav",
  "breaks": "breaks/amen.wav",
  "atmos": "atmos/pad-1.wav"
}
```

> Replace `yourusername` with your actual GitHub username.

You can also assign multiple variations to one name:

```json
"bd": ["bd/kick-1.wav", "bd/kick-2.wav", "bd/kick-heavy.wav"]
```

### 4. Create / Update the GitHub Repository

1. Go to GitHub and create a new **public** repository named `psyTech-public` (or use your existing one).
2. Upload the contents:
   - Drag and drop the `strudel.json` + entire `wav/` folder into the repo root via the GitHub web UI, **or**
   - Use git:
     ```bash
     git add .
     git commit -m "Add PsyTech sample library + strudel.json"
     git push
     ```

### 5. Load the Samples in Strudel

In any Strudel REPL (strudel.cc), run:

```javascript
samples('github:yourusername/psyTech-public')

// Now play your samples:
s("bd sd hh*8")
s("breaks/0")     // First break (zero-indexed)
s("atmos")
```

That's it. Your samples are now available with short, clean names.

---

## Daily Workflow (Updating Samples)

1. Add or replace files inside the `wav/` folder locally.
2. Re-run the generator (from inside `wav/`):
   ```bash
   npx --yes @strudel/sampler --json > ../strudel.json
   ```
3. Commit and push the changes to GitHub.
4. In Strudel, force a fresh load (to bypass browser cache):
   ```javascript
   // Do NOT append ?version= to the github: shortcut — it breaks URL parsing.
   // Use the full raw URL instead:
   samples('https://raw.githubusercontent.com/yourusername/psyTech-public/main/strudel.json?version=2')
   ```

---

## Alternative: Quick Local Testing (No GitHub)

If you just want to test samples quickly without uploading:

- In the Strudel REPL, open the **sounds** panel (right side)
- Click **Import sounds folder**
- Select your local `wav/` folder

Samples will appear under the **user** tab and can be referenced immediately (great for prototyping before publishing).

You can also run a local server:
```bash
cd wav
npx @strudel/sampler
```
Then in Strudel:
```javascript
samples('http://localhost:5432/')
```

---

## Tips & Troubleshooting

- **Caching**: Strudel (and browsers) aggressively cache samples. Bump the version on the **full raw URL** (not the `github:` shortcut): `samples('https://raw.githubusercontent.com/.../strudel.json?version=2')`.
- **`#` in filenames**: Encode as `%23` in `strudel.json` paths (e.g. `F%23min` not `F#min`), or rename files to avoid `#`.
- **Pitched samples** (e.g. piano, melodic instruments): Use nested objects in `strudel.json`:
  ```json
  "piano": {
    "c3": "piano/c3.wav",
    "d3": "piano/d3.wav",
    "e3": "piano/e3.wav"
  }
  ```
  Then play with `note("c3")` or `n("0")`.
- **Multiple packs**: You can load several libraries in one session:
  ```javascript
  samples('github:yourusername/psyTech-public')
  samples('github:tidalcycles/Dirt-Samples')
  ```
- **File formats**: Strudel supports `.wav`, `.mp3`, and `.ogg`.
- **Large libraries**: GitHub has generous limits. For very large collections, consider splitting into multiple repos.

---

## Summary – One-Line Magic

Once set up, loading your entire PsyTech sample library is as simple as:

```javascript
samples('github:yourusername/psyTech-public')
```

This is the cleanest, most maintainable, and most "Strudel-native" way to work with custom samples.

---

**Maintained for PsyTechArt** • Optimized for Strudel live coding workflows

*Last updated: June 2026*