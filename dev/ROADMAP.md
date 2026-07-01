# sandbox-sec - Roadmap

Front-loaded: the hardest and most security-critical work (the Linux enforcement
core and the fail-closed guarantees) comes first. No mechanism ships without its
adversarial test suite. Nothing is deferred past 1.0 to "make the deadline."

v0.1.0 â€” Scaffold + capability model. The platform-independent core: Policy,
Restriction, Capability, and EnforcementReport types. Deny-all baseline. Builder
API. Platform detection. Introspection skeleton (`active_restrictions()`). The
fail-closed and no-silent-degradation contracts encoded as types, so unsupported
restrictions are unrepresentable-as-silently-allowed at the API level. (This is the
scaffold stage stamps.)

v0.2.0 â€” Linux seccomp-bpf backend. Syscall allow/deny lists, argument filtering,
seccomp user-notification path for advanced supervised syscalls. Curated syscall
presets (MINIMAL, COMPUTE, IO). Adversarial tests: forbidden syscalls must EPERM.
This is the hard core; it lands first.

v0.3.0 â€” Linux Landlock + namespaces. Landlock path-based filesystem access control
(read/write/exec allowlists). Unprivileged user namespaces plus mount, pid, net,
ipc, uts, and cgroup isolation. Filesystem-confinement and resource-visibility
adversarial tests.

v0.4.0 â€” Windows backend. Job Objects (memory/CPU/process limits, breakaway
prevention), restricted/lowbox tokens, AppContainer isolation where available.
Confinement and limit-enforcement tests on Windows CI.

v0.5.0 â€” OpenBSD + macOS backends. OpenBSD pledge (promise sets) and unveil
(path exposure). macOS sandbox profile (Seatbelt) as best-effort with an explicit
deprecated-API caveat surfaced through introspection. Per-platform test matrices.

v0.6.0 â€” Unified policy compiler + profiles. One Policy compiles to the strongest
available enforcement per platform, engaging multiple mechanisms (defense in depth).
Composable layering and named presets (network-off, read-only-fs, no-exec,
compute-only). Cross-platform conformance tests: the same Policy enforces
equivalently everywhere it is supported, and errors clearly where it is not.

v0.7.0 â€” Hardening pass. TOCTOU audit of the apply/exec path. Fuzzing of seccomp
filter generation and policy compilation. A dedicated escape-attempt suite (attempt
to break out of each mechanism). Optional audit logging of every enforcement
decision. Threat-model document.

v0.8.0â€“v0.9.0 â€” Performance, ergonomics, docs. Filter-compilation and apply-path
perf. Per-platform runnable examples (confine self, spawn confined child, plugin
host). Complete API docs with security caveats stated inline, not buried.

v1.0.0 â€” Frozen after a mandatory pre-1.0 security audit: fail-closed proven on every
platform, no-silent-degradation proven, every mechanism has passing adversarial and
escape-attempt tests, TOCTOU-clean, fuzz-clean, threat model published. API frozen.

