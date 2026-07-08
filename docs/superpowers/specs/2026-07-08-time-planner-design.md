# Time Planner — Design Spec

## Overview

A daily time-planning single-page web app with "box + block" mental model.
Users prepare reusable task blocks and place them into daily boxes.
Nightly planning ritual → daytime execution → nightly review (batch mark done/failed).

## Tech Stack

- Single HTML file (HTML + CSS + vanilla JS)
- localStorage for persistence
- No build step, no dependencies

## Naming

- App title: `xxx的Time` (editable by user, monospace bold font)
- User can click the title to edit their name

## Visual Style

- **visionOS Spatial UI** — light mode, glass morphism, deep Z-axis layering
- **Background**: 3 giant blurred colorful orbs (light pink/blue/purple) floating slowly
- **Cards**: Hardcore glass — `rgba(255,255,255,0.25)` + `backdrop-filter: blur(30px) saturate(160%)` + `1px solid rgba(255,255,255,0.5)` + multi-layer soft shadows + inset top highlight
- **Task blocks**: Recessed/sunken (inset shadow) inside floating glass cards — card floats, tasks sink in
- **3D Tilt**: Cards tilt ±8° following mouse cursor, with diagonal glossy sheen sweep
- **Tab bar**: Unified glass capsule, selected tab is a raised white pill

## Views (Tab Switching)

| Tab | Purpose |
|-----|---------|
| 📅 **三日视图** | Default. Yesterday · Today · Tomorrow, 3 columns |
| 📊 **七日概览** | Last 7 days overview + trend sparklines |
| 📈 **周记录** | Week-based summary statistics |
| 🧱 **方块库** | Block template library (CRUD) |

## Data Model

### Block Template (方块)
```js
{
  id: string,
  name: string,
  duration: number,       // minutes
  category: string,       // work | study | exercise | rest | custom
  priority: 'high' | 'medium' | 'low',
  notes: string,
  color: string,          // derived from category
  createdAt: string       // ISO date
}
```

### Daily Box Entry (箱子中的方块实例)
```js
{
  id: string,
  blockTemplateId: string | null,  // null = ad-hoc
  date: 'YYYY-MM-DD',
  name: string,
  duration: number,
  category: string,
  priority: string,
  startTime: string | null,   // 'HH:mm'
  status: 'pending' | 'done' | 'failed',
  order: number
}
```

### Daily Summary (computed)
```js
{
  date: string,
  totalPlanned: number,     // minutes
  doneCount: number,
  failedCount: number,
  pendingCount: number,
  byCategory: { [cat]: number }
}
```

## Storage Schema

- `timeplanner_blocks` — array of block templates
- `timeplanner_entries_YYYY-MM-DD` — per-day box entries
- `timeplanner_username` — user's name for the title

## Key Interactions

1. **Three-day view**: Blocks shown in 3 columns, vertically stacked, idle gaps visible
2. **Add block to box**: Click "+ add" placeholder → pick from library or create ad-hoc
3. **Batch review**: "全部完成" / "全部破裂" buttons on today's card
4. **Export image**: html2canvas or similar to capture card/stat view as PNG
5. **Editable title**: Click "xxx的Time" → inline edit name → persists to localStorage

## Image Export

- Use a lightweight approach (DOM → canvas → PNG download)
- Both daily box view and summary statistics view exportable

## Verification

1. Open `index.html` in browser
2. Create block templates in 方块库
3. Add blocks to tomorrow's box
4. Mark today's blocks as done/failed
5. Check 七日概览 for trends
6. Export image and verify PNG downloads
7. Refresh page — verify all data persists
8. Edit title name — verify it saves
