# What's next ?

## Context

FOV is a student project developed as part of our **EIP**. Active development runs until **February 2027**. This document explains what happens after that date.

---

## 1. What we're shipping before February 2027

We have four pillars, all planned for delivery before the EIP defense:

| **Pillar**              | **Scope**                                     | **Status target** |
| :---------------------- | :-------------------------------------------- | :---------------- |
| **person — COMPTES**    | User accounts, personal space, authentication | Delivered         |
| **groups — SOCIAL**     | Follows, interactions between users           | Delivered         |
| **forum — DISCUSSION**  | Live chat between users                       | Delivered         |
| **build — MAINTENANCE** | Stability, updates, dependency management     | Delivered         |

These are the features we commit to. Anything else is out of scope.

---

## 2. After February 2027

**We will probably not maintain the project actively.**

We're students. After the EIP, we move on to jobs, internships, or other projects. We don't have a company or funding behind FOV. Any maintenance will be **best-effort**, driven by personal motivation or by new contributors.

That doesn't mean the project is dead. We're leaving it in a state where it can be forked or continued by anyone — including us, if we come back to it.

---

## 3. What we leave in the repo

- Full source code (backend, frontend and OBS fork).
- Docker Compose setup that runs the whole stack locally.
- `README.md` with setup, architecture overview, and known limitations.
- `SECURITY.md` with our current security posture and known gaps.
- Documentation for the OBS-FOV build (install, start a stream, configure the endpoint).
- Release artifacts for Windows, macOS, and Linux.
- **Open GitHub issues** covering known bugs, unimplemented features, and security improvements.
- `good first issue` **labels** for newcomers.
- `help wanted` **labels** for issues that need specific expertise (HLS, SRT, multi-track sync).
- CI running on pull requests (build + basic tests).

---

## 4. If you want to take over

- **Fork freely.** The license allows it.
- **Open a pull request** if you want to contribute back. Reviews are best-effort — expect weeks, not days.
- **Want to become a maintainer?** Open an issue titled `Maintainer application` with who you are, what you want to maintain, and how much time you can commit per month.

If we become unreachable and a fork becomes the de-facto upstream, we'd appreciate it if its maintainers state that clearly in their README and keep following the same security process.

---

## 5. Archiving

We are **not planning to archive**. Archiving would make the repo read-only, blocking future contributions.

We'd only consider it after **24 months of complete inactivity**, and only cleanly:

- Final commit tagged `final-archive`.
- A notice at the top of `README.md` pointing to any active fork.
- Handover notes (section 6) up to date.
- Dependency snapshot documented.

---

## 6. Honest status report for whoever picks it up

### What works well

- Multi-stream HLS playback with track synchronization.
- Layout editor (position, size, z-order, visibility, volume) with per-stream persistence.
- OBS-FOV build producing multi-track SRT streams.
- JWT-based authentication.
- Docker Compose setup for local dev.

### What is fragile

- **Security** has known gaps — see `SECURITY.md`.
- **OBS fork** tracks an old upstream. Rebuilding against a newer OBS is non-trivial.

### Where to start

1. Read `README.md` and `SECURITY.md`.
2. Run the stack with Docker Compose.
3. Pick a `good first issue`.
4. Run `npm audit` on both backend and frontend.
5. Migrate layout persistence to PostgreSQL — small, high-value.

---

## 7. TL;DR

- **Shipping before February 2027**: COMPTES, SOCIAL, DISCUSSION, MAINTENANCE.
- **Maintained after?** Probably not actively. Best-effort.
- **Archived?** No, unless 24 months of inactivity.
- **Forkable?** Yes, under the project license.
- **Documented for takeover?** Yes — this file, `README.md`, `SECURITY.md`, and open issues.

If you pick it up: welcome, and good luck.

---

**Document Version**: 1.0  
**Last Updated**: October 2026  
**Applies from**: February 2027 (after EIP defense)
