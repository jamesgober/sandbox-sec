# sandbox-sec - Project Plan

> The design, strategy, and decision record for `sandbox-sec`, alongside the existing [`ROADMAP.md`](./ROADMAP.md) and [`DIRECTIVES.md`](./DIRECTIVES.md). A living document: every design decision, idea, or open question about sandbox-sec is written here or in the roadmap before it is acted on.
>
> **Status:** scaffold, `0.1.0`.

---

## Contents

1. [Purpose](#1-purpose)
2. [Where it sits in the service stack](#2-where-it-sits-in-the-service-stack)
3. [Contracts](#3-contracts)
4. [Mechanisms per platform](#4-mechanisms-per-platform)
5. [What the service stack needs from it](#5-what-the-service-stack-needs-from-it)
6. [Threats](#6-threats)
7. [What it does not cover](#7-what-it-does-not-cover)
8. [Ecosystem and dependencies](#8-ecosystem-and-dependencies)
9. [Open decisions](#9-open-decisions)
10. [Decision log](#10-decision-log)

---

## 1. Purpose

sandbox-sec confines processes and code: what system calls they may make, which paths they may touch, which networks they may reach, how much they may consume, and what they may spawn. One fail-closed API over each operating system's own facilities, so a policy written once is enforced as strongly as each platform allows, and never silently weaker.

---

## 2. Where it sits in the service stack

```
shield-sec (policy: what each part of a service may do)
     │
     ▼
sandbox-sec (mechanism: how the OS enforces it)
     │
     ▼
seccomp · Landlock · namespaces · pledge/unveil · Seatbelt · job objects · AppContainer
```

sandbox-sec is the mechanism; `shield-sec` is the policy that decides which restrictions a network-facing process, a storage process, or a guard process gets, and when they are applied. sandbox-sec stays general purpose: it is just as useful for a plugin host or a build sandbox.

---

## 3. Contracts

Already set by the roadmap and kept here as binding:

- **Fail closed.** A restriction that cannot be enforced is an error, never a silent pass.
- **No silent degradation.** Applying a policy returns an `EnforcementReport` that says exactly which restrictions are enforced by which mechanism; a policy may require that every restriction be enforced.
- **Defence in depth.** Where several mechanisms can enforce one restriction, all of them are engaged.
- **Irreversible once applied.** A process cannot loosen its own sandbox.

---

## 4. Mechanisms per platform

| Restriction | Linux | OpenBSD | macOS | Windows |
|---|---|---|---|---|
| system calls | seccomp-bpf | pledge | Seatbelt profile | process mitigation policies, restricted token |
| filesystem paths | Landlock | unveil | Seatbelt profile | AppContainer, ACL-restricted token |
| network | Landlock network rules (ABI 4+), network namespace, seccomp socket filtering | pledge (`inet` omitted) | Seatbelt profile | AppContainer capabilities, (WFP: R&D) |
| process creation | seccomp, `PR_SET_NO_NEW_PRIVS` | pledge (`proc`, `exec` omitted) | Seatbelt | job object (no breakaway, active process limit) |
| resources | cgroups, rlimits | rlimits | rlimits | job object limits |
| visibility | pid, mount, ipc, uts namespaces | | | AppContainer |

---

## 5. What the service stack needs from it

Requirements from `shield-sec` and the web/service stack, so they are planned rather than discovered late:

| Need | Used for | Roadmap fit |
|---|---|---|
| a **network-facing** preset: sockets yes, data directory no, exec no | the process that parses hostile bytes | v0.6.0 presets |
| a **storage** preset: data directory yes, network no (local socket only), exec no | the process that owns data files | v0.6.0 presets |
| a **guard** preset: minimal syscalls plus fanotify | shield-sec's data-directory guard experiment | v0.6.0, after R&D |
| path allowlists that include a single local socket | the compartment boundary between network and storage processes | v0.3.0 Landlock |
| network rules by port (Landlock ABI 4) with a reported fallback | shield-sec's outbound allowlist | v0.3.0 |
| apply to self after setup (bind ports, open files, then lock down) | servers that need privileges only at startup | v0.1.0 model |
| spawn a confined child | compartments launched by a supervisor | v0.2.0 to v0.4.0 |

---

## 6. Threats

| Threat | Defence |
|---|---|
| code execution in a parser reaching files or the network | path and network restrictions |
| escalation through new executables or setuid binaries | exec restrictions, no-new-privileges |
| resource exhaustion by confined code | resource limits |
| a sandbox that quietly does nothing on some platform | fail closed, enforcement report |

---

## 7. What it does not cover

- Kernel vulnerabilities: a sandbox reduces the reachable kernel surface but cannot fix the kernel.
- Already-open handles: restrictions apply to new operations; a process must close what it does not need before locking down (the apply-after-setup model makes this explicit).
- macOS Seatbelt is a deprecated API; its use is best effort and reported as such.

---

## 8. Ecosystem and dependencies

| Crate | Relationship |
|---|---|
| `shield-sec` | main consumer: policy presets and when to apply them |
| `net-io` | local sockets for the compartment boundary (no code dependency) |
| `audit-trail` | optional audit of enforcement decisions (roadmap v0.7.0) |

---

## 9. Open decisions

| Id | Question | Recommendation |
|---|---|---|
| O-1 | Publish to crates.io at 0.1.0 to claim the name? | yes |
| O-2 | Windows Filtering Platform for network restriction (needs administrator rights at install): in scope? | R&D only until 1.0 |

---

## 10. Decision log

| Id | Date | Decision | Reason |
|---|---|---|---|
| D-001 | 2026-10-13 | sandbox-sec is the mechanism layer under shield-sec's policy | one owner per concern; sandbox-sec stays general purpose |
| D-002 | 2026-10-13 | Presets for network-facing, storage, and guard processes planned in v0.6.0 | the service stack's compartments depend on them |
