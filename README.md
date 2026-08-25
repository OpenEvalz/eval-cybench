# eval-cybench

**Cybench: Capture-The-Flag Cybersecurity Challenges**

**Paper:** https://arxiv.org/abs/2408.08926

Tests language models on cybersecurity skills using 39 of 40 practical, professional-level
challenges taken from cybersecurity competitions, designed to cover various difficulty levels
and security concepts. The motp challenge is excluded due to GPL licensing.

## At a glance

| | |
|---|---|
| Upstream | [`src/inspect_evals/cybench`](https://github.com/UKGovernmentBEIS/inspect_evals/tree/main/src/inspect_evals/cybench) |
| Group | Cybersecurity |
| Total samples | 39 |
| Execution class | `sandbox-k8s` |
| Cost class | `high` |
| Flags | k8s-ready · sandboxed · needs internet |
| Tags | Agent |

### Tasks

| Task | Samples |
|---|---|
| `cybench` | 39 |

### External assets

- `direct_url` — `https://www.haproxy.org/download/2.8/src/haproxy-2.8.1.tar.gz` (pinned)
- `direct_url` — `https://raw.githubusercontent.com/andyzorigin/cybench/{SHA}/` (pinned)
- `direct_url` — `https://raw.githubusercontent.com/LosFuzzys/GlacierCTF2023_writeups/{SHA}/` (pinned)

This eval declares `supports_k8s: true` and is one of only six in inspect_evals that do. `k8s/values.yaml` holds the sandbox profile and egress allowlist.

## Running one problem

OpenEvalz is problem-level: the atomic unit is a single sample, not the whole eval.

```bash
inspect eval inspect_evals/cybench \
  --sample-id "<sample-id>" \
  --model openai-api/trustedrouter/<model> \
  --token-limit 200000
```

> **Two things that bite here, both verified in Inspect's source.**
>
> 1. **`--cost-limit` does not work on this routing path.** Inspect only records cost when its
>    pricing table resolves the model, and `_model_info.py` strips only `azure|bedrock|vertex`
>    prefixes — so `trustedrouter/<model>` never resolves and the cap silently never binds. The
>    real spend cap is enforced **server-side by TrustedRouter** via the delegated key's
>    `limit_microdollars` and spend window. Use `--token-limit` as the in-process bound.
> 2. **`--sample-id` matches with `fnmatch`.** A glob silently selects many samples and only warns.
>    Always pass a literal id.

## Reproducibility

`bundle.template.json` is the contract. A run that cannot emit a complete bundle does not publish.
Every image is pinned by `sha256` digest and every dataset by revision.

## Licensing

OpenEvalz wrapper code in this repository is **Business Source License 1.1** (see `LICENSE`) —
Licensor Lore Hex Corp, Change Date four years from publication, Change License Apache 2.0, no
Additional Use Grant. Same terms as TrustedRouter. Source-available, not open source: you may read,
modify and make non-production use of it, but production use needs a commercial licence
(licensing@openevalz.com).

**The packaged evaluation is NOT relicensed.** The task code, dataset and container images come from
upstream under their own terms — inspect_evals is MIT (UK AI Security Institute), and individual
datasets and images carry their own, sometimes unstated, licences. BSL covers only the OpenEvalz
packaging around them. See `NOTICE.md`, which must be completed before this repo publishes anything.
