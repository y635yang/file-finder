# File Finder

A local web app that lets you find files on your computer using plain English (or Mandarin Chinese), then opens a pre-filled draft in Mail with the files attached — no password setup required.

## What it does

1. You type a natural language query, e.g.:
   - *"Can you find me the purchase agreement under my OneDrive Life and Home Buying folder?"*
   - *"帮我找一下OneDrive里面Life and Home Buying文件夹下的购房协议"*
2. Claude finds matching files (PDF, Word, PowerPoint) and ranks them by relevance
3. You click **+ Add** to select one or more files — you can run multiple searches and keep adding
4. Click **Compose email →**, fill in the recipient and an optional message
5. Click **Open in Mail** — your Mail app opens with a draft and all attachments ready to send

---

## How the search works

File Finder uses a two-pass search strategy to save time and cost:

### Pass 1 — Filename match (fast)
Claude looks at the **names of all files** in the folder and checks whether any of them clearly match your query. For example, if you search for *"feasibility study"* and a file is named `28 Main FEASIBILITY STUDY - Updated v04.pdf`, that's an obvious match.

- If confident matches are found → results are returned **immediately**, no files are opened
- The result will say **"matched by filename"**
- This is fast and uses very few tokens

### Pass 2 — Content match (only if Pass 1 finds nothing)
If no filename matches are found, the app **opens each file and reads its content**:
- PDFs → text is extracted page by page
- Word documents (.docx) → paragraphs are read
- PowerPoint files (.pptx) → text from every slide is read

Claude then reads those text snippets and finds files whose **content** matches your query — even if the filename gives no hint. Each result shows a short **excerpt** explaining why it matched.

- The result will say **"searched by file content"**
- This takes a few extra seconds since files are being read
- Up to 20 files are read, with up to 3,000 characters extracted per file

This two-pass approach means most searches finish quickly (Pass 1), and the slower content search only runs when it's actually needed.

---

## Requirements

- macOS (uses Apple Mail to compose emails)
- Python 3.9 or later
- An Anthropic API key (free to sign up at [platform.anthropic.com](https://platform.anthropic.com))

---

## Setup (one-time)

### Step 1 — Download the project

Click the green **Code** button on this page → **Download ZIP**, then unzip it anywhere you like (e.g. your Desktop or Documents folder).

### Step 2 — Get an Anthropic API key

1. Go to [platform.anthropic.com](https://platform.anthropic.com) and sign up / log in
2. Click **Console** (top right) → **API Keys** in the left sidebar
3. Click **Create Key**, give it a name, and copy the key — it looks like `sk-ant-...`

### Step 3 — Add your API key

1. Inside the project folder, find the file called `.env.example`
   - If you can't see it, press **Cmd + Shift + .** in Finder to show hidden files
2. Make a copy of it and rename the copy to `.env` (remove the word "example")
3. Open `.env` with TextEdit
4. Replace `your_api_key_here` with the key you copied
5. Save and close

### Step 4 — Install dependencies

1. Open the **Terminal** app (press **Cmd + Space**, type "Terminal", press Enter)
2. Paste this command and press Enter:

```bash
pip3 install fastapi uvicorn anthropic python-dotenv pypdf python-docx python-pptx
```

### Step 5 — Start the app

Each time you want to use File Finder:

1. Open **Terminal**
2. Paste this (update the path if you saved the folder somewhere else):

```bash
cd ~/Desktop/file-finder && python3 -m uvicorn app:app --port 8000
```

3. Open your browser and go to **http://localhost:8000**

To stop the app, go back to Terminal and press **Ctrl + C**.

---

## Tips

- **Pin your folders** — use the Search scope panel to limit searches to specific locations (e.g. your OneDrive or Documents). This makes searches faster and more accurate.
- **Multiple attachments** — search multiple times with different queries and click **+ Add** on each file you want. They all go into one email.
- **Folder names** — you can mention folder names naturally in your query. The app fuzzy-matches them against your real folders, so minor spelling differences are fine.
- **Works in Chinese** — queries in Mandarin Chinese work just as well as English.
- **Keep Terminal open** — the app only runs while the Terminal window is open. If you close it, just run the start command again.

---

## Troubleshooting

| Problem | Fix |
|---|---|
| Localhost won't open | Terminal was closed — run the start command again |
| "Failed to parse query" | Try rephrasing your request more clearly |
| "Could not find folder" | Check the folder name spelling or pin the root folder in Search scope |
| Mail doesn't open | Make sure Apple Mail is set up on your Mac |
| `pip3: command not found` | Install Python from [python.org](https://python.org) |
