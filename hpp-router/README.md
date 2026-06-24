# HPP Router docs — content for Docusaurus

This folder contains the **HPP Router** developer documentation as Docusaurus-ready Markdown. It was authored per the doc plan in [hpp-router issue #93](https://github.com/hpp-io/hpp-router/issues/93).

The Docusaurus site itself is set up separately. **Drop this `hpp-router/` folder into the site's `docs/` directory** (or adjust `docPath`), and the sidebar will populate from the `_category_.json` files and per-page `sidebar_position` frontmatter.

## Structure

```
hpp-router/
├── _category_.json            # "HPP Router" top-level category (position 3)
├── introduction.md            # slug: /hpp-router
├── quickstart.md              # REST / @hpprouter/sdk / OpenAI SDK 3-way
├── authentication.md          # apikey header + Bearer
├── models-and-pricing.md      # /llm/v1/models + token-based billing
├── smart-routing.md           # hpprouter/auto
├── guides/
│   ├── _category_.json
│   ├── chat-completions.md
│   ├── streaming.md
│   ├── vision-multimodal.md
│   ├── image-generation.md
│   ├── quota-and-usage.md
│   ├── errors.md
│   └── openai-sdk.md
├── api-reference/
│   ├── _category_.json
│   ├── consumer-api.md         # Markdown summary of the contract
│   └── consumer-v1.yaml        # OpenAPI spec (single source of truth)
└── client-sdk/
    ├── _category_.json
    └── typescript.md           # @hpprouter/sdk
```

## Source of truth

All content is grounded in the `hpp-router` repository:

| Page | Source in `hpp-router` |
| --- | --- |
| Introduction / Quickstart / Chat / Vision / Image | `README.md` |
| Smart Routing | `docs/smartrouting/dynamic-routing-policy-20260521.md` |
| Quota & Usage | `openapi/consumer-v1.yaml`, `docs/quota-cache/quota-cache-en.md` |
| API Reference | `openapi/consumer-v1.yaml` |
| Client SDK | `packages/sdk/README.md` |

When the source changes, update the corresponding page here. The `consumer-v1.yaml` is a **copy** of `hpp-router/openapi/consumer-v1.yaml` — keep them in sync (ideally via a build step in the Docusaurus repo).

## Integration notes for the Docusaurus maintainer

- **Base URL** used throughout: `https://router.hpp.io`.
- **Interactive API reference:** wire `api-reference/consumer-v1.yaml` into an OpenAPI plugin (e.g. `docusaurus-plugin-openapi-docs` or a Scalar embed) to get a "try-it" reference. The Markdown `consumer-api.md` is a fallback summary.
- **Sidebar placement:** the issue proposes "HPP Router" as a top-level section between *Ecosystem* and *Building on HPP*. `_category_.json` uses `position: 3` as a starting point — adjust to match the final sidebar.
- **Cross-links:** internal links use relative `.md` paths so they work both on GitHub and in Docusaurus.

## Known gaps / follow-ups

### `audit`

Issue #93 listed an `audit` topic among the original five pages. The public `hpp-router` sources only mention a "blockchain audit trail" at a high level, and the issue itself proposed reducing `audit` to an overview plus a link to the Portal `/app/audit` page. No standalone `audit.md` was written here to avoid documenting behavior that isn't specified in the available sources. Add one once canonical audit content exists.

### Portal sign-up & API key issuance (TODO — needs Portal source)

The Router Portal (in development) lets users **sign up and issue API keys directly from the Portal**, not only through HPP Hub. The current docs describe key issuance via HPP Hub only, which is incomplete. This should be revised once the Portal source is available to reference.

Pages to update:

- `authentication.md` — "Getting an API key": add the Portal path alongside HPP Hub.
- `quickstart.md` — Prerequisites: mention obtaining a key via the Portal.
- `introduction.md` — the "API keys are issued through HPP Hub" sentence.
- (Optional) a new Portal guide page covering sign-up, key management, and the dashboard.

Source to reference: the Router Portal app in the `hpp-router` repo (e.g. `public/`, `src/dashboard.js`, and Portal routes such as `/admin`, `/playground/`).
