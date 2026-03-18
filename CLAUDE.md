# CLAUDE.md – KLW Vietnam QC Dashboard

## Project overview
Multi-user QC error reporting web app for KLW Vietnam garment factory.
Staff submit daily defect reports. Managers view aggregated dashboard.
Single file: `index.html` — all HTML, CSS, and JS in one file.

## Live URL
**https://klw-qc-report.vercel.app** (permanent, never changes)

## Stack
- Frontend: Vanilla HTML/CSS/JS, Be Vietnam Pro font
- Libraries: Chart.js, Supabase JS SDK, SheetJS (xlsx) — all via CDN
- Database: Supabase
- Hosting: Vercel
- Repo: GitHub `vundaclaude/klw-qc-report` (branch: master)

## Accounts & credentials
Credentials are stored in Claude memory (not here — kept off GitHub for security).
| Service    | Detail |
|------------|--------|
| Vercel project ID | `prj_94LIbJIAvsMSoy5b2p9RuYnnInSL` |
| Vercel team ID | `team_CfUsjeoXFMGUkcQ5AdLtVvX2` |
| Supabase project ref | `tfgwycupqfzgjcddzgar` |
| Supabase URL | `https://tfgwycupqfzgjcddzgar.supabase.co` |
| GitHub account | `vundaclaude` — GitHub CLI at `C:/Program Files/GitHub CLI/gh.exe` |

## Database — Supabase
Table: `qc_reports`
| Column | Type | Notes |
|--------|------|-------|
| id | uuid | PK, auto |
| created_at | timestamptz | auto |
| staff_name | text | required |
| report_date | date | auto |
| data | jsonb | 10×10 matrix [process][defect] |
| row_totals | jsonb | array[10] — total per process |
| col_totals | jsonb | array[10] — total per defect type |
| grand_total | int | sum of all errors |

RLS: enabled, open policy (no login required to read/write)

## App — 3 tabs
1. **Nhập Báo Cáo** — staff form: enter name + 10×10 matrix → submit → saves to Supabase → shows dashboard with charts + detail table + Excel download
2. **Dashboard Quản Lý** — manager view: 4 KPI cards, time filters (all/today/7d/30d), 2 bar charts, staff ranking table, heatmap, export summary Excel
3. **Lịch Sử Báo Cáo** — all reports list, click to view detail modal + Excel download

## Data — 10 processes (rows)
1. Cắt vải
2. May cổ áo
3. Ghép vai
4. May tay áo
5. Ráp tay vào thân
6. May sườn
7. Lai gấu
8. Đính nhãn / Tag
9. Kiểm tra & Ủi
10. Đóng gói

## Data — 10 defect types (columns L1–L10)
L1: Lỗi đường may | L2: Lệch đường may | L3: Nhăn / Rúm | L4: Hở mũi chỉ | L5: Chỉ thừa
L6: Bỏ sót mũi may | L7: Vải bị hỏng | L8: Lỗi màu sắc | L9: Kích thước sai | L10: Hàng bẩn

## Mobile design
- Bottom navigation bar on mobile (≤640px) — app-style tabs
- Touch-friendly inputs (40px height)
- Full-width buttons, stacked vertically
- Swipe hint shown above tables
- Modal slides up from bottom (sheet style)
- Staff table hides less important columns on mobile
- KPI grid 2×2

## Design tokens
```
--navy:   #0f1535   (header, table headers)
--navy2:  #1a2250
--blue:   #4f6ef7   (primary accent)
--red:    #e74c3c   (errors)
--green:  #27ae60   (success, Excel button)
--orange: #e67e22   (warnings)
--bg:     #f0f2f8   (page background)
```

## Deploy workflow (run after every change to index.html)
Tokens are stored in Claude memory — Claude handles this automatically.
Steps:
1. `git add index.html && git commit -m "description" && git push`
2. Upload file to Vercel API (`/v2/files`) with SHA
3. Create deployment via Vercel API (`/v13/deployments`)
4. Wait ~10s for READY, then assign alias `klw-qc-report.vercel.app`

## Code rules
- Keep everything in `index.html` — no separate files unless explicitly requested
- Language: Vietnamese UI, English/Vietnamese variable names
- Do not add new CDN libraries without good reason
- CSS in `<style>`, JS in `<script>`
- To add a process: edit `congDoan[]` array
- To add a defect type: edit `loaiLoi[]` array
