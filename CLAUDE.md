# CLAUDE.md

**`AGENTS.md` is the house rules for this repo — read it first.** This file does not restate them beyond the two
that must never be missed; two homes for one rule is how a correction reaches one and not the other.

Posts and changes reach `main` only through a pull request, and nobody pushes to `main`. A merge to `main` deploys
to production, so it waits for Michal.

Every factual claim in a post needs a source the reader can check; never invent numbers, quotes, prices or results.

## Working here

- Structure and commands: `AGENTS.md` (Layout, Making a change). Frontmatter table and the `/blog` prefix: `README.md`.
- The build is one file, `build.mjs`; the tests in `test/build.test.mjs` are the executable specification. Change
  behaviour and the test together.
- `middleware.js` and `vercel.json` repeat the `/blog` prefix; keep them in step with `BASE_PATH`.
- The blog is reached through a rewrite in the `3A-DAO/3adao-landing` project (`3adao.org/blog/*` →
  this project). Moving the prefix means changing that rewrite too.
- Do not add a LICENSE; the owner decides.
