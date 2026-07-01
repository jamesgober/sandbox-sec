# sandbox-sec - API

Capability-based and fail-closed. The baseline denies everything; the caller grants
the minimum; build() errors if the platform cannot enforce what was requested.

    use sandbox_sec::{Sandbox, Syscalls, Namespace};

    // Start fully closed, grant the minimum, build (errors on unenforceable policy).
    let sandbox = Sandbox::deny_all()
        .allow_read("/usr/lib")
        .allow_read("/etc/ssl/certs")
        .allow_write("/tmp/work")
        .allow_syscalls(Syscalls::COMPUTE)     // curated seccomp allowlist
        .deny_network()                        // net namespace / pledge / job
        .memory_limit(512 * 1024 * 1024)       // cgroup / job object
        .isolate(Namespace::PID | Namespace::MOUNT)
        .build()?;                             // Err(Unenforceable { restriction, platform })

    // Confine the current process (pre-exec), or spawn an already-confined child.
    sandbox.apply()?;
    // let child = sandbox.spawn("untrusted-bin", &args)?;

    // Introspection: assert the REAL enforced state, not the requested one.
    for r in sandbox.active_restrictions() { /* audit / test */ }

Opt-in degradation is explicit and per-restriction:

    Sandbox::deny_all()
        .deny_network().best_effort()   // caller knowingly accepts weaker enforcement here
        .build()?;

