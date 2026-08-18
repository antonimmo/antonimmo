# Draft: profile README

Proposed replacement for the repo-root `README.md` (the one that renders on
github.com/antonimmo). **Not applied** — review, edit the voice to taste, then copy over.

Notes on choices:
- Leads with proof, not adjectives. Numbers do the persuading.
- The PhD is framed as *why you engineer the way you do*, not as a credential flex.
- Every section ends pointing somewhere: `anton.immo`, LinkedIn, or email.
- No badge walls, no contribution-streak widgets, no trophy cases. They read as noise to the
  senior engineers who will actually be reviewing you.

---

```markdown
# César Antonio Pérez Quintana

**Senior Backend Engineer** — distributed systems, applied cryptography, scientific computing.
Santander, Spain · remote, overlapping EU & US hours.

I build backends for the part of the curve where things break: credential issuance and
verification for live events at 3,000 users/minute, embedded wallets for people who have never
touched crypto, and an unattended pipeline that chews through 100 GB of ocean data every day and
is expected to be right by morning.

I came to backend work through numerical ocean models and HPC, which is why I reach for a
profiler before a rewrite.

### What I work on

- **Distributed backends** — Go, Python, TypeScript. REST, gRPC, event-driven systems, rules engines.
- **Applied cryptography** — ECDSA signing, verifiable credentials, key management, on-device verification.
- **Data at scale** — TB-scale ETL over scientific datasets (Dask, xarray, netCDF); AWS Batch, Step Functions, Lambda.
- **HPC & numerical modelling** — Fortran/OpenMP, ROMS / Delft3D / SFINCS, containerized for reproducibility.

### Selected work

| Project | What it was |
|---|---|
| **Glyph** (Yuga Labs) | Open-source embedded wallet + SDK. Owned end to end; 2k internal users in month one, +5k via third-party integrations. |
| **IH-TESEO** (IHCantabria) | Re-engineered a Fortran/OpenMP pollutant-transport model: ~50× more particles, up to 4× faster, no accuracy loss. |
| **tokenproof credentials** | Go microservices issuing, signing and verifying credentials — 50k accounts across 10k devices, sub-2s eligibility checks at the door. |

### Elsewhere

**[anton.immo](https://anton.immo)** · [LinkedIn](https://linkedin.com/in/antonioquintana3) · antonimmo@gmail.com
```

---

## Also do this (2 minutes, matters as much as the README)

Your pinned repositories are currently mostly forks — `xmitgcm`, `Duke`, `viem`,
`DefinitelyTyped`, `public-wallet-connectors`. A pinned fork signals "I cloned this once,"
which is the opposite of the intent.

**Unpin the forks. Pin, in this order:**

1. The portfolio repo (once it exists) — the freshest self-authored thing you have
2. `glyph-sdk-react`
3. `opencerta-icerta-issuer-cli`
4. `ebc-wk-spectral-analysis`
5. + each demo repo as it ships

If a repo is worth pinning but has a thin README, fix the README before pinning it — a pinned
repo with no explanation is worse than an empty slot.
