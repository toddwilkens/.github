# toddwilkens/.github

Personal shared CI infrastructure — public for portability across repos. Contains reusable GitHub Actions composite actions and reusable workflows. No org secrets live here.

---

## What's here

### `.github/actions/lychee`

Composite action wrapping `lycheeverse/lychee-action@v2`. Fails the CI step on internal 4xx/5xx links; posts external link failures as a non-blocking PR comment.

```yaml
- uses: toddwilkens/.github/.github/actions/lychee@v1
  with:
    scan-target: "out/**/*.html"        # optional, default covers Next.js .next/
    skip-urls: |                        # optional, newline-separated regex patterns
      https://example.com
    fail-on-internal-404: "true"        # optional, default true
    external-link-comment: "true"       # optional, default true
```

### `.github/actions/json-ld-lint`

Composite action that walks built HTML, extracts `<script type="application/ld+json">` blocks, validates JSON syntax, and expands each block against the schema.org context using the `jsonld` processor. Errors point to the specific page and `@type` that failed.

```yaml
- uses: toddwilkens/.github/.github/actions/json-ld-lint@v1
  with:
    build-dir: ".next"     # optional, default .next
    strict: "true"         # optional, default true (fail on error)
    schema-org-version: "latest"  # optional, informational
```

### `.github/workflows/lighthouse-on-preview.yml`

Reusable workflow (`workflow_call`) that runs Lighthouse CI against a Vercel preview URL and enforces a configurable SEO score threshold (default 0.95).

```yaml
# In your repo's .github/workflows/lighthouse.yml:
on:
  deployment_status:

jobs:
  lighthouse:
    if: github.event.deployment_status.state == 'success'
    uses: toddwilkens/.github/.github/workflows/lighthouse-on-preview.yml@v1
    with:
      preview-url: ${{ github.event.deployment_status.target_url }}
      seo-threshold: 0.95
    secrets:
      vercel-bypass-secret: ${{ secrets.VERCEL_AUTOMATION_BYPASS_SECRET }}
```

---

## Versioning

- **`@main`** — bleeding edge, always the latest commit. Use in development or when you want automatic updates.
- **`@v1`** — pinned major tag. Consumers should pin `@v1` for stability. The tag is force-updated on non-breaking changes within the v1 API surface; a new `@v2` tag is cut for breaking changes.

**Recommendation:** pin `@v1` in production workflows.

---

## Community health files

This repo also serves as the source of organization/user-default community health files (`CODE_OF_CONDUCT.md`, `SECURITY.md`, `CONTRIBUTING.md`, `SUPPORT.md`). GitHub automatically surfaces these for repos in the `toddwilkens` namespace that don't have their own. None are added yet — out of scope for the initial cut.
