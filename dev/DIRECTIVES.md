# sandbox-sec - Directives

Security correctness outranks every other concern, including ergonomics and
performance. The rules below are non-negotiable and the roadmap enforces them.

Fail closed. A default-constructed sandbox denies all syscalls, all filesystem
access, all network access, and drops all capabilities. Access is only ever added,
never removed from an open baseline.

No silent degradation. If the platform cannot enforce a requested restriction, the
builder returns an error naming the restriction and the platform. A caller who
knowingly accepts weaker enforcement must opt in explicitly per-restriction
(`.best_effort()`); there is no global "just make it work" that hides gaps.

Defense in depth. Where a platform offers multiple mechanisms (seccomp AND Landlock
AND namespaces on Linux), the policy engages all applicable ones rather than relying
on a single control. One bypassed layer must not mean total escape.

Least privilege, applied atomically. Restrictions are installed before the untrusted
code runs (pre-exec, or immediately on self-confinement) with no window in which the
code runs less confined than requested. TOCTOU windows are treated as bugs.

Total auditability. The sandbox reports exactly which restrictions are active at
runtime (introspection), so callers and tests can assert the real enforced state
rather than the requested one.

Every restriction is proven, not assumed. A test that installs a filter without then
attempting the forbidden action and asserting it fails is worthless. Every backend
ships adversarial tests that actively try to do the denied thing and confirm the
kernel blocks it.

forbid(unsafe) at the crate root; unsafe is permitted only in isolated, audited,
individually-justified platform-FFI modules, never in the policy engine.

