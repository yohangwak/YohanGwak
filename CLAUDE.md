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

- **No application build, automated test suite, or package manager.** Before
  committing, run the relevant manual checks below for the files you changed.
- **The repository workflow does not run on pushes or pull requests.** It is
  manual-only (`workflow_dispatch`). A PR may still show an external Pullfrog
  review check; treat that separately from application verification.
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

Check only what the change affects. For `README.md`, verify the parts that
fetch:

```bash
# every badge/image returns 200, and its logo slug actually resolved
badge_probe="$(mktemp -t yohangwak-badge.XXXXXX)"
trap 'unlink "$badge_probe"' EXIT
while IFS= read -r u; do
  if code="$(curl --silent --show-error --location --connect-timeout 5 \
      --max-time 20 --output "$badge_probe" --write-out '%{http_code}' "$u")"; then
    printf '%s logo=%s  %s\n' "$code" \
      "$(grep -c 'data:image' "$badge_probe")" "$u"
  else
    printf '%s logo=not-checked  %s\n' "${code:-curl-error}" "$u"
  fi
done < <(grep -o 'https://img.shields.io[^)]*' README.md)
# want: 200 logo=1 for each. logo=0 means the slug is wrong — the badge
# renders, but with no icon, which is easy to miss by eye.

# every link resolves
while IFS= read -r u; do
  code="$(curl --silent --show-error --location --connect-timeout 5 \
    --max-time 20 --output /dev/null --write-out '%{http_code}' "$u")" || \
    code="${code:-curl-error}"
  printf '%s  %s\n' "$code" "$u"
done < <(grep -oE 'https://[^)" ]+' README.md)
```

If `AGENTS.md` or `CLAUDE.md` changed, also confirm that Git records
`AGENTS.md` with mode `120000` and the exact target `CLAUDE.md`:

```bash
git ls-files -s AGENTS.md CLAUDE.md
git cat-file -p :AGENTS.md
```

Then open <https://github.com/yohangwak> after merging and look at it once —
the profile column is narrower than the repo file view, so wide tables and long
unwrapped lines look different there.

## Updating the profile

Commit to `main` (or merge a PR into it). The profile page updates immediately;
images may be cached by GitHub's image proxy (`camo`) for a while.
