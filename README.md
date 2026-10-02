# SYNTAXpractice
syntax for beginners
# 🌳 Syntax Twin

**Your Digital Learning Companion for Introduction to Syntax**

Syntax Twin is a single-page web application designed for undergraduate linguistics students taking an Introduction to Syntax course. It combines interactive study material, four graded test sets, SVG-based syntax tree diagrams, and a teacher dashboard — all in one self-contained HTML file with no build step or framework dependencies.

---

## Features

### 📚 Learn Syntax
An expandable topic-wise study guide covering 14 core syntax topics:

1. What is Syntax?
2. Syntax and Traditional Grammar
3. A Brief Evolution of Syntax
4. Structure: Sentence, Clause, Phrase, Word
5. Lexical Categories (Parts of Speech)
6. A Simple Grammar with Lexical Categories
7. IC Analysis (Immediate Constituent Analysis)
8. Phrase Structure Grammar (PSG)
9. PSG Limitations
10. TG Grammar (Transformational Generative Grammar)
11. Transformations
12. The Four Operations (Addition, Deletion, Movement, Substitution)
13. Competence and Performance
14. Deep Structure and Surface Structure

Each topic includes key points, examples, and study tips.

### 📝 Set 1 — Multiple Choice Questions (25 Qs)
Standard MCQ format covering all syntax topics. Immediate feedback with correct/incorrect indicators and explanations.

### 🌲 Set 2 — IC Analysis (10 Multistep Qs)
Interactive multistep questions where students build **Immediate Constituent** tree diagrams step by step. Each correct answer reveals the next level of the tree. Includes an ambiguity question (Q10) with dual tree readings.

### 🔤 Set 3 — Phrase Structure Grammar (10 Multistep Qs)
Multistep questions on PSG rules, phrase types, and recursion. Features progressively revealed tree diagrams including deeply nested PP structures (Q10 with 3 levels of embedding).

### 🔄 Set 4 — TG Grammar (10 Multistep Qs)
Multistep transformational derivation exercises covering passivisation, question formation, negation, and other transformations.

### 🌲 SVG Tree Diagrams
- **Proper diagonal branching** — linguistically accurate hierarchical trees, not boxy CSS layouts
- **Progressive reveal** — tree nodes appear step by step as students answer correctly
- **Color-coded nodes:**
  - 🟣 Purple (`#7c3aed`) — Root node (S)
  - 🟢 Teal (`#0d9488`) — Phrasal nodes (NP, VP, PP, AP, AdvP)
  - 🔵 Blue (`#2563eb`) — Lexical nodes (Det, N, V, A, P, etc.)
- **Animated entry** — new nodes fade in with a pop animation
- **Dual-tree support** — ambiguous sentences show both readings side by side
- **Responsive** — wide trees scroll horizontally on small screens

### 📊 Teacher Dashboard
- Password-protected login (credentials: `admin` / `syn2024`)
- View all student submissions from Google Sheets
- Summary statistics: total submissions, average score, pass rate
- Filterable data table with Roll No, Name, Set, Score, Grade, Date

### Additional Features
- 🌙 **Automatic dark mode** via `prefers-color-scheme`
- 📱 **Mobile-responsive** layout
- 💾 **Local storage** backup of results (`synTwinResults` key)
- ☁️ **Google Sheets integration** for centralised result collection
- 🎯 **Detailed results screen** with score ring, grade, topic-wise strength bars, and personalised encouragement
- ⏸️ **Incomplete test saving** — exiting mid-test saves partial progress

---

## Project Structure

```
syntax-twin/
├── index.html              # Complete app (HTML + CSS + JS, single file)
├── google-apps-script.js   # Google Apps Script backend code
└── README.md               # This file
```

The entire application lives in `index.html` — no external CSS or JS files, no npm dependencies at runtime, no build step. Just open the file in a browser.

---

## Setup

### 1. Deploy the App

Simply host `index.html` on any static file server or open it directly in a browser:

- **GitHub Pages:** Push to a repo and enable Pages from Settings
- **Local:** Double-click `index.html` to open in any modern browser
- **Any web server:** Nginx, Apache, Netlify, Vercel — just serve the single HTML file

### 2. Set Up Google Sheets Backend (Optional)

The app can send student results to a Google Sheet for teacher review.

1. Create a new **Google Sheet**
2. Rename the first sheet tab to **"Results"**
3. Add these headers in Row 1:

   | Roll No | Name | Set | Score | Grade | Good | Errors | Unanswered | Total | Status | Date |
   |---------|------|-----|-------|-------|------|--------|------------|-------|--------|------|

4. Go to **Extensions → Apps Script**
5. Paste the contents of `google-apps-script.js` and save
6. Click **Deploy → New deployment → Web app**
   - Execute as: **Me**
   - Who has access: **Anyone**
7. Copy the Web App URL
8. Open `index.html` and replace the `SHEET_URL` constant with your URL:

   ```javascript
   const SHEET_URL = 'https://script.google.com/macros/s/YOUR_SCRIPT_ID/exec';
   ```

> Without this setup, the app still works fully — results are saved to `localStorage` instead of being sent to a sheet. Students will not notice any difference.

---

## Teacher Dashboard

Access the dashboard from the home screen by tapping **📊 Teacher Dashboard**.

- **Username:** `admin`
- **Password:** `syn2024`

The dashboard pulls data from the connected Google Sheet via `doGet()`. It displays a summary row (total submissions, average score, pass rate) and a sortable table of all student attempts.

> To change the password, search for `syn2024` in `index.html` and replace it.

---

## Technical Details

| Aspect | Detail |
|--------|--------|
| **Architecture** | Single-page application (SPA) with screen-based navigation |
| **Rendering** | Vanilla HTML/CSS/JS — no frameworks |
| **Tree diagrams** | SVG generated at runtime via `buildTreeSVG()` |
| **Theme** | CSS custom properties on `:root` with `@media(prefers-color-scheme:dark)` |
| **Data submission** | `fetch()` with `mode: 'no-cors'` and `Content-Type: text/plain` to Google Apps Script |
| **Local storage** | `localStorage` key `synTwinResults` stores JSON array of past results |
| **Backend** | Google Apps Script `doPost()` / `doGet()` for Sheets read/write |
| **Browser support** | Any modern browser (Chrome, Firefox, Safari, Edge) |

### Tree Diagram Algorithm

The SVG tree renderer uses a three-phase layout:

1. **Measure** (bottom-up) — Recursively compute subtree widths, filtering out nodes not yet revealed
2. **Position** (top-down) — Assign x,y coordinates to each node, centering children under parents
3. **Draw** (recursive) — Generate SVG elements: diagonal `<line>` connectors first (for correct z-order), then `<rect>` pills and `<text>` labels

Progressive reveal is controlled by a `step` (s) value on each node in the tree data. As students answer each step correctly, `_stepsDone` increments and `updateTree()` re-renders with more nodes visible.

---

## Customisation

### Adding/Editing Questions

Questions are defined as JavaScript arrays near the top of the `<script>` block:

- `mcqData` — Set 1 MCQs (25 items)
- `icData` — Set 2 IC Analysis multistep (10 items)
- `psgData` — Set 3 PSG multistep (10 items)
- `tgData` — Set 4 TG Grammar multistep (10 items)

**MCQ format:**
```javascript
{ topic:'lexical_cat', q:'Which test is most reliable for determining lexical category?',
  options:['Meaning','Morphology','Distribution','Etymology'], correct:2,
  explain:'Distribution (where a word can appear) is the most reliable test.' }
```

**Multistep format with tree:**
```javascript
{ topic:'ic_analysis', q:'Analyse: "The tall boy kicked the ball"',
  context:'Identify constituents step by step.',
  tree:{ l:'S', s:0, c:[
    { l:'NP', s:1, c:[{ l:'Det', s:3, w:'The' }, { l:'A', s:3, w:'tall' }, { l:'N', s:3, w:'boy' }] },
    { l:'VP', s:1, c:[{ l:'V', s:4, w:'kicked' }, { l:'NP', s:4, c:[{ l:'Det', s:5, w:'the' }, { l:'N', s:5, w:'ball' }] }] }
  ]},
  steps:[
    { prompt:'First split: what are the two main ICs of S?', options:['NP + VP','V + NP','Det + N + V'], correct:0, explain:'S → NP + VP' },
    // ... more steps
  ]
}
```

Tree node properties:
- `l` — Label (e.g. 'S', 'NP', 'Det')
- `s` — Step number at which this node becomes visible (0 = always visible)
- `c` — Array of child nodes
- `w` — Terminal word (leaf nodes only, displayed in italics below the label)

### Changing the Theme

Edit the CSS custom properties in the `:root` and `@media(prefers-color-scheme:dark)` blocks at the top of the file. Key variables: `--accent`, `--bg`, `--card`, `--text`, `--good`, `--avg`, `--weak`.

### Adding Learn Topics

Add entries to the `LEARN_DATA` array and register the topic key in the `TOPICS` object for icon/name mapping.

---

## License

This project was created for educational use in an Introduction to Syntax university course.

---

## Credits

Built with ❤️ for linguistics students.

- **Tree diagram engine:** Custom SVG renderer with diagonal branching
- **Backend:** Google Apps Script + Google Sheets
- **Design:** Responsive, accessible, dark-mode-ready interface
