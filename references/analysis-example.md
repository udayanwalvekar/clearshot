# Worked Example — Dashboard Screenshot (Level 1+2)

This example shows what a completed Level 1+2 analysis looks like for a typical SaaS dashboard.

## Level 1: Map

**5x5 Grid Layout:**
| | Col 1 | Col 2 | Col 3 | Col 4 | Col 5 |
|---|---|---|---|---|---|
| **Row 1** | Logo + nav (sidebar) | Top bar: search input | Top bar: breadcrumb | Top bar: empty | Avatar + bell icon |
| **Row 2** | Nav item: Dashboard (active, #3B82F6 bg) | Metric card: "Revenue" $24.5k | Metric card: "Users" 1,247 | Metric card: "Conv. Rate" 3.2% | Metric card: "Churn" 1.1% |
| **Row 3** | Nav item: Analytics | Line chart (revenue over time, ~60% width) | Line chart cont. | Bar chart (signups by source, ~40% width) | Bar chart cont. |
| **Row 4** | Nav item: Settings | Table: "Recent Transactions" header | Table row 1: "Acme Corp — $1,200" | Table row 2: "Globex — $840" | Table row 3: "Initech — $2,100" |
| **Row 5** | Nav: collapsed bottom section | Table row 4 | Table row 5 (cut off — scroll) | Empty | Pagination: "1 of 12" |

**Key elements by section:**

**Sidebar (Col 1, full height):** Fixed ~240px width. #111827 bg. Logo top-left ~32px. Nav items: 14px medium weight, #9CA3AF default, active item has #3B82F6 bg with white text, 6px radius. 12px vertical gap between items. Feels clean but the active state is the only color — slightly flat.

**Metric cards (Row 2, Cols 2-5):** 4-up grid, ~16px gap. White bg, 8px radius, subtle shadow (0 1px 3px rgba(0,0,0,0.1)). Label: 12px #6B7280 uppercase tracking-wide. Value: 28px #111827 semibold. Trend indicator: small green/red arrow + percentage. Cards feel weightless — almost floating. The hierarchy reads clearly: number dominates, label orients, trend confirms.

**Charts (Row 3):** Two charts side by side, ~16px gap. White card wrapper same as metrics. Line chart uses #3B82F6 primary with #93C5FD fill opacity ~10%. Bar chart uses alternating #3B82F6 and #E5E7EB. Axis labels: 11px #9CA3AF. Grid lines: #F3F4F6. Clean but the bar chart axis labels feel slightly cramped.

**Table (Rows 4-5):** White card, same radius/shadow. Header row: 12px #6B7280 uppercase, #F9FAFB bg. Body rows: 14px #374151, 48px row height, #E5E7EB bottom border. Alternating row bg would help scanability — currently all white.

Eye goes: metric cards first (big numbers), then chart, then table. Sidebar recedes (dark, narrow). Hierarchy is clear and intentional.

## Level 2: System

**Colors:**
- Page bg: #F3F4F6
- Card/surface bg: #FFFFFF
- Primary action: #3B82F6
- Text primary: #111827
- Text secondary: #6B7280
- Text muted: #9CA3AF
- Border/divider: #E5E7EB
- Success: #10B981
- Destructive: #EF4444

Palette is cohesive — standard Tailwind-like neutral + blue primary. No clashing accents.

**Typography:**
- Headings: 18-28px, semibold (600), normal case
- Body: 14px, regular (400), ~1.5 line-height
- Caption/label: 11-12px, medium (500), uppercase with tracking
- Font family: Inter or system sans-serif

Type scale is intentional — three clear tiers with consistent weight differentiation.

**Spacing and shape:**
- Spacing pattern: comfortable (16px card gaps, 12px internal padding, 24px section margins)
- Border radius: subtle (8px cards, 6px buttons/inputs, full-round avatars)
- Density: comfortable — not cramped, not wasteful

Spacing is consistent across sections. The 8px radius on everything creates visual unity.
