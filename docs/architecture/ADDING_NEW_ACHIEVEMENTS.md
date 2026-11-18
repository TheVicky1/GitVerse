# Adding a New Achievement to the Catalog

This guide walks through adding a badge that GitHub has released to `internal/catalog`, the single source of truth for every known achievement. It describes the code as it is today.

## How the catalog is used

- `internal/catalog/catalog.go` holds the `Achievements` slice (display order), the `Highlights` slice, and helpers such as `BySlug` and `KindTitle`.
- `internal/catalog/images.go` holds the `Images` map (slug to badge art URLs) and the `Image(slug, tier)` helper.
- `internal/report` builds a report row for every catalog entry, and `internal/counters` supplies live counts.
- `cmd/gendocs` reads the catalog to generate the README badge table, `site/achievements.json`, `site/llms.txt`, `site/llms-full.txt`, the sitemap, the site pages and the Markdown pages under `web/markdown/`.

A badge that GitHub adds and the catalog does not know yet still appears in the CLI, because earned badges are read from the profile (see the package comment in `catalog.go`). Adding it to the catalog gives it its description, tiers, FAQ and art.

## The `Achievement` struct

```go
type Achievement struct {
	Slug string `json:"slug"`
	Name string `json:"name"`
	Kind Kind   `json:"kind"`
	// Tiers are the counts for x1, x2, x3, x4. A single-tier badge has one entry.
	// 0 marks a threshold nobody has measured yet.
	Tiers     []int   `json:"tiers"`
	Unit      string  `json:"unit,omitempty"`
	HowToEarn string  `json:"howToEarn"`
	Details   string  `json:"details"`
	Counter   Counter `json:"counter"`
	// NoCounterReason says why a tiered badge shows its tier only.
	NoCounterReason string `json:"noCounterReason,omitempty"`
	FAQ             []QA   `json:"faq,omitempty"`
}
```

| Field | Meaning |
| :--- | :--- |
| `Slug` | The badge's URL slug on GitHub, as in `/users/<user>/achievements/<slug>/detail`. Also the key into `Images` and the site page path. |
| `Kind` | `Earnable`, `Disabled` (tested by GitHub, switched off), `Retired` (a one-off event that is over) or `Internal` (GitHub staff only). |
| `Tiers` | Counts for x1 to x4. One entry means a single-tier badge (`IsTiered()` is true only for more than one entry). Use `0` for a threshold nobody has measured. |
| `Unit` | What the tiers count, for example `"merged pull requests"`. |
| `Counter` | How progress to the next tier is measured: `CounterNone`, `CounterMergedPRs`, `CounterStars` or `CounterAnswers`. |
| `NoCounterReason` | Why a tiered badge with `CounterNone` shows its tier only. `internal/report` adds it as a note for tiered earnable badges without a counter. |
| `HowToEarn`, `Details`, `FAQ` | Text shown on the site and in generated Markdown. `FAQ` is a list of `QA{Q, A}`. |

## Step by step

### 1. Add the definition to `catalog.go`

Append an `Achievement` to the `Achievements` slice. The slice order is the display order, so keep entries of the same `Kind` together.

```go
{
	Slug: "example-badge", Name: "Example Badge", Kind: Earnable,
	Tiers: []int{1, 10, 25, 50}, Unit: "example things", Counter: CounterNone,
	NoCounterReason: "GitHub's counting rule is not public: tier only, no counter",
	HowToEarn:       "Do the example thing",
	Details:         "What the badge recognizes, using only facts you verified.",
	FAQ: []QA{
		{"How many example things do you need?",
			"1 for base (x1), 10 for bronze (x2), 25 for silver (x3), and 50 for gold (x4)."},
	},
},
```

(The slug and numbers above are placeholders for illustration, not a real badge.)

Guidelines taken from the existing code:

- State only what is verified against live profiles. `LastVerified` is "the date the catalog was last checked against live profiles"; update it when you re-check.
- For a single-tier badge use `Tiers: []int{1}`.
- If GitHub's counting rule is not public, use `CounterNone` and set `NoCounterReason`.
- A badge whose art GitHub removed is `Disabled` (see step 2).

### 2. Add the art to `images.go`

Add an entry to the `Images` map in `internal/catalog/images.go`, in the order default, bronze, silver, gold (a single-tier badge has one URL):

- Earnable, retired and internal badges use GitHub's own art from `https://github.githubassets.com/assets/<slug>-<tier>-<hash>.png`. The catalog stores these as URLs, not as image files.
- `Disabled` badges use the community copies under `communityArt`, which is pinned to one commit.

`Image(slug, tier)` returns the default art for tier 0 or 1 and clamps to the last image above that. `internal/catalog/images_test.go` checks that every catalog badge has art for every tier, with the expected URL prefix for its `Kind`.

### 3. Counting logic (only if the badge has a counter)

Most new badges need no code: use `CounterNone` and the report shows GitHub's official tier. A counter needs more than a catalog entry:

1. Add a new `Counter` constant in `catalog.go`.
2. Handle it in `internal/report/report.go`. The `switch a.Counter` in `(*builder).row` dispatches to `pullShark`, `starstruck` and `galaxyBrain`; a new counter needs its own case and method.
3. Add the data source it needs to `internal/counters` (the `Source` interface and its implementations). Note that without a token, the public implementation supports only merged PR and star counts, and `Facts` returns nil.
4. Handle the new counter in `progressText` in `cmd/gendocs/main.go`, which describes each counter on the generated pages.

Do this only when the public API can actually count what GitHub counts; the existing `Pair Extraordinaire` and `Finishing Touch` entries show the "tier only" alternative.

### 4. Regenerate the generated files

```bash
go run ./cmd/gendocs
```

This rewrites the README sections, the SVGs under `docs/`, the `site/` files, `vercel.json` and the Markdown pages under `web/markdown/` from the catalog.

### 5. Verify

```bash
go test ./...
go run ./cmd/gendocs -check
```

`-check` fails if any generated file is out of date; it is also run in CI (`.github/workflows/test.yml`). The repository also has JavaScript tests: `node --test internal/webui/jstest/*.test.mjs`.

## Common pitfalls

- **Editing generated files by hand.** Files under `site/` that `cmd/gendocs` writes (for example `achievements.json`, `llms.txt`, `sitemap.xml`) and the generated README sections will be overwritten and fail `-check`. Change the catalog, then regenerate.
- **A missing `Images` entry.** The art test fails for any catalog badge without art at every tier.
- **Thin FAQ or unverified claims.** Existing entries give concrete, checked answers (thresholds, whether private activity counts, whether it is still earnable). Do the same, and do not guess thresholds: use `0` in `Tiers` for unmeasured ones.
- **Wrong `Kind`.** `Kind` decides the section the badge appears in (`Kinds` is the display order) and whether `internal/report` computes progress; only `Earnable` badges get counters or official-tier notes.
