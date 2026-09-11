# YohanGwak — GitHub Profile Repository

Special GitHub repository whose `README.md` is rendered on the profile page at
<https://github.com/yohangwak>. GitHub treats a repository named exactly like
its owner as the owner's profile repository.

**This repo has exactly one product: `README.md`.** Everything else here exists
to support it.

## Structure

```
/
├── README.md                    # ← the profile page. Public. Edit this.
├── CLAUDE.md                    # this file (AGENTS.md is a symlink to it)
├── AGENTS.md → CLAUDE.md
└── .github/
    └── workflows/
        └── pullfrog.yml         # manual (workflow_dispatch) agent runner
```

## Working here

- **No build, no test suite, no package manager.** The repo is Markdown only —
  there is nothing to install or compile, and nothing to run before committing.
- **No CI runs on pull requests.** Nothing verifies a change for you.
- **`.github/workflows/pullfrog.yml` is vendor-managed** (`DO NOT EDIT EXCEPT
  WHERE INDICATED`). It only triggers on `workflow_dispatch`, so it never fires
  on push or PR. Leave it alone unless the task is specifically about it.
- Adding workflows is discouraged — a profile page earns nothing by spending
  Actions minutes. If one is truly needed: `ubuntu-*` runners only (`macos-*`
  bills 10×, `windows-*` 2×), and no `schedule:` — a cron that nobody watches
  burns the allowance quietly, especially when it fails.

## Editing README.md — it is a public page

Anything committed here is visible to everyone who opens the profile. So:

- No credentials, tokens, raw IPs, private repo names, or business details.
- Keep every claim true and checkable. This is a person's public introduction.
- Give every image an `alt` text — profile pages get read by screen readers too.
- Prefer no widget over a widget that breaks. Third-party stat cards
  (`github-readme-stats` and friends) run on shared, rate-limited instances; a
  429 shows up as a broken image at the top of the profile. GitHub already
  draws the contribution graph directly below the README.

### Verifying a change

Render is the only thing that can break, so check the parts that fetch:

```bash
# every badge/image returns 200, and its logo slug actually resolved
for u in $(grep -o 'https://img.shields.io[^)]*' README.md); do
  curl -s -o /tmp/b.svg -w '%{http_code} ' "$u"
  echo "logo=$(grep -c 'data:image' /tmp/b.svg)  $u"
done
# want: 200 logo=1 for each. logo=0 means the slug is wrong — the badge
# renders, but with no icon, which is easy to miss by eye.

# every link resolves
for u in $(grep -oE 'https://[^)" ]+' README.md); do
  echo "$(curl -s -o /dev/null -w '%{http_code}' "$u")  $u"
done
```

Then open <https://github.com/yohangwak> after merging and look at it once —
the profile column is narrower than the repo file view, so wide tables and long
unwrapped lines look different there.

## Updating the profile

Commit to `main` (or merge a PR into it). The profile page updates immediately;
images may be cached by GitHub's image proxy (`camo`) for a while.
