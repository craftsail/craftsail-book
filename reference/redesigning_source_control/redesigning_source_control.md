# Notes: Redesigning Source Control at Anthropic

**Original:** [Redesigning Source Control at Anthropic](https://www.anthropic.com/engineering/redesigning-source-control) — Anthropic Engineering Blog, 2026-10-13

**Authors:** Nick G. and the Anthropic source control team

> These are reading notes and a paraphrased summary, not the original text. Please read the full article at the link above.

## 1. Background: source control became the bottleneck

- Claude now writes and iterates on code autonomously at large scale, so the amount of code changing hands has grown sharply.
- Between May and September 2026, Anthropic's daily merges went from about 2,500 to more than 30,000.
- Writing code stopped being the bottleneck. Source control took its place.
- Anthropic built its own system, **Scion**, and moved off Git in about five months.

## 2. Where Git breaks down in a growing monorepo

- History alone is approaching 100 GB, and the compressed working copy is over 25 GB.
- CI runs about 25 million checkouts a day. Fetching naively would mean roughly 600 PB of traffic.
- Mirrors, partial clones and snapshots all helped, but together they became too hard to run.
- The root problem: Git is decentralized and built to work offline. Anthropic's agents are always online.
- The question that drove the redesign: what would source control look like if it were designed for always-online agents from the start?

## 3. Six design principles

1. **Clients are always online.** Offline-first design is no longer needed.
2. **The server does the heavy lifting.** History, merges and blame live on the server. Clients keep only a bounded cache.
3. **Full API coverage.** Every source control query can be made through the API, so a local repo isn't required.
4. **Horizontal scaling.** Data is spread across servers, avoiding central bottlenecks like Git packfiles.
5. **Cheap, lock-free operations.** The cost of a commit depends on how big the change is, not how big the repo is. Server-side transactions are supported.
6. **Checkouts are optional and sparse.** Virtual and partial checkouts are supported, and some workflows use only the API.

## 4. How Scion works

### Data model
- **Changeset** is the basic unit. It records the files that changed relative to the first parent, plus pointers to content-addressed blobs.
- Writes need no packfiles, no locks and no ref coordination, so throughput is high.
- **Derived data** (file blame, path history, skip lists over the commit graph) is computed separately and recursively. New kinds of derived data can be added without changing clients.

### Serving architecture
- A small, horizontally scaled fleet of Scion servers serves every API: diff, ancestry, blame and so on.
- Because data is content-addressed, it can be cached aggressively without coordination.
- Checkouts go through three tiers:
  - **scion-daemon** (one per node): local object store, started from periodic snapshots
  - **scion-cache** (one per region): serves objects from memory
  - **scion-server** (the source of truth): reads from Spanner or cloud storage
- Each cache tier hits more than 99% of the time.

### Results
- Infrastructure footprint is about 200x smaller than the old Git mirror setup.
- CI and research checkouts are about 100x faster, down from minutes to seconds.
- CI alone saves about 25% of its CPU.

## 5. Lessons from building Scion

- **A small team, moving fast:** seven engineers built Scion, the code review tool "Antique" and related systems in five months. They finished the migration while merge volume grew 10x.
- **The engineer's role is changing:** engineers think Claude is better at writing raw code. Human value has moved to high-level design, trade-offs and decisions. A key skill is "telling Claude no", which keeps it from breaking constraints or over-engineering.
- **Quality:** the team relied on hermetic simulation, fuzzing and rate limiting. Claude also found non-obvious optimizations, such as a missing index on ancestry queries.
- **Monorepo security:** access is controlled per repo and per directory, review-approval rules are complex, and trivial changes are separated from sensitive ones for human reviewers.
- **Operations:** Claude instances act as "superfans", each watching one component closely. They catch subtle problems that normal alerts miss.
- **Rethinking standard practice:** a traditional merge queue doesn't fit at agent scale. The team moved to post-merge and pre-deploy automated testing, and Claude resolves concurrent conflicts quickly.

## 6. Impact on the wider ecosystem

- Scion is not a general Git replacement. It only makes sense for very large monorepos, with tens of thousands of engineers or swarms of agents.
- The authors expect this kind of problem to become common within 6–12 months.
- Anthropic chose to share its thinking rather than open-source the code.
- Suggestions for Git platforms: add server-side RPCs without changing the client protocol, improve the negotiation protocol, and make sparse and shallow features work better together.
- The authors want to work with GitHub, GitLab and the open-source community on this.
