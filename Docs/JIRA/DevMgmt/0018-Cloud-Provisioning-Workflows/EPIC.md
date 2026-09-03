# EPIC-0018 — Cloud Provisioning Workflows

**Phase:** D · **Depends on:** container images from 0005 onward · **Blocks:** nothing

## Intent

Manually-triggered, numbered `Setup` and `Destroy` workflows across three clouds:
**CloudFormation on AWS, Terraform on Azure and GCP.**

```
<CLOUD>-<NNNN>-<Setup|Destroy>-<Target>.yml     workflow_dispatch only
```

## The number is the dependency order

Setup runs ascending, Destroy descending. On AWS, cross-stack **exports** make CloudFormation refuse an
out-of-order delete — the rule stops being a convention and becomes an API guarantee. Terraform gives no
such guard across separate states, so **the Azure and GCP Destroy workflows implement the check themselves**,
written once in the reusable workflow.

## The budget shapes the targets

Per the $1 posture: **GCP Cloud Run is the primary target** and Azure Container Apps the second, both scaling
to zero. **AWS is demo-only** — provision, demonstrate, destroy the same day. Images come from `ghcr.io`, so
no cloud needs its own registry block. Postgres is a single free-tier instance reachable from all three, and
the cloud corpus is sampled, not the full 300k.

## Success measures

- A full `Setup-All` then `Destroy-All` cycle on AWS leaves no orphaned resources and costs under a dollar.
- No workflow triggers on push. Every `Destroy` requires a typed confirmation matching the environment name.
- The same container images run on all three clouds; only environment variables differ.

## Features

| ID | Name | Stories |
|----|------|---------|
| FEAT-0018-01 | Naming, Numbering & Reusable Skeleton | 3 |
| FEAT-0018-02 | OIDC Federation & Safety Rails | 3 |
| FEAT-0018-03 | AWS CloudFormation Blocks | 3 |
| FEAT-0018-04 | Azure & GCP Terraform Blocks | 3 |
| FEAT-0018-05 | Composite Setup-All / Destroy-All | 2 |

## Out of scope

Production hardening, multi-region, autoscaling policy, disaster recovery.
