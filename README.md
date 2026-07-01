<h1 align="center">
    <img width="99" alt="Rust logo" src="https://raw.githubusercontent.com/jamesgober/rust-collection/72baabd71f00e14aa9184efcb16fa3deddda3a0a/assets/rust-logo.svg">
    <br>
    <b>sandbox-sec</b>
    <br>
    <sub><sup>CROSS-PLATFORM PROCESS SANDBOXING</sup></sub>
</h1>

<div align="center">
    <a href="https://crates.io/crates/sandbox-sec"><img alt="Crates.io" src="https://img.shields.io/crates/v/sandbox-sec"></a>
    <a href="https://crates.io/crates/sandbox-sec"><img alt="Downloads" src="https://img.shields.io/crates/d/sandbox-sec?color=%230099ff"></a>
    <a href="https://docs.rs/sandbox-sec"><img alt="docs.rs" src="https://img.shields.io/docsrs/sandbox-sec"></a>
    <a href="https://github.com/jamesgober/sandbox-sec/actions"><img alt="CI" src="https://github.com/jamesgober/sandbox-sec/actions/workflows/ci.yml/badge.svg"></a>
    <a href="https://github.com/rust-lang/rfcs/blob/master/text/2495-min-rust-version.md"><img alt="MSRV" src="https://img.shields.io/badge/MSRV-1.85%2B-blue"></a>
</div>

<br>

<div align="left">
    <p>
        sandbox-sec confines untrusted code. It restricts which syscalls a process may make, which files and directories it may touch, whether it may reach the network, how much memory and CPU it may consume, and which kernel resources it can see ??? behind one capability-based API that compiles a single policy down to the strongest enforcement each platform offers.  The design premise is that a sandbox which fails open is worse than no sandbox at all, because it grants false confidence. So sandbox-sec is fail-closed by default: a fresh sandbox denies everything, the caller explicitly grants back the minimum, and if the running platform cannot actually enforce a requested restriction the build errors loudly rather than silently letting the code run unconfined. Silent degradation is treated as a security defect, not a convenience.  Prior art is real but narrow. extrasafe and seccompiler cover seccomp only. sandlock and koda are capable but Linux-only (Landlock / seccomp-bpf). No crate unifies process sandboxing across Linux, OpenBSD, Windows, and macOS with a clean, introspectable, fail-closed API. That unification is the wedge, and it is exactly the primitive a Rust code-execution service, CI runner, plugin host, or multi-tenant runtime needs ??? and the primitive AVA needs to run untrusted code.
    </p>
    <br>
    <hr>
    <p>
        <strong>MSRV is 1.85+</strong> (Rust 2024 edition).
    </p>
    <blockquote>
        <strong>Status: pre-1.0, in active development.</strong> The public API is being designed across the 0.x series and frozen at <code>1.0.0</code>. See <a href="./CHANGELOG.md"><code>CHANGELOG.md</code></a>.
    </blockquote>
</div>

<hr>
<br>

## Installation

```toml
[dependencies]
sandbox-sec = "0.1"
```

<br>

## Status

This is the <code>v0.1.0</code> scaffold: structure, tooling, and quality gates are in place; the implementation lands across the 0.x series per the <a href="./dev/ROADMAP.md"><code>ROADMAP</code></a> and <a href="./docs/API.md"><code>docs/API.md</code></a>.

<hr>
<br>

## Contributing

See <a href="./dev/DIRECTIVES.md"><code>dev/DIRECTIVES.md</code></a> for engineering standards and the definition of done. Before a PR: `cargo fmt --all`, `cargo clippy --all-targets --all-features -- -D warnings`, and `cargo test --all-features` must be clean.

<br>

<div id="license">
    <h2>License</h2>
    <p>Licensed under either of</p>
    <ul>
        <li><b>Apache License, Version 2.0</b> &mdash; <a href="./LICENSE-APACHE">LICENSE-APACHE</a></li>
        <li><b>MIT License</b> &mdash; <a href="./LICENSE-MIT">LICENSE-MIT</a></li>
    </ul>
    <p>at your option.</p>
</div>

<div align="center">
  <h2></h2>
  <sup>COPYRIGHT <small>&copy;</small> 2026 <strong>James Gober <me@jamesgober.com>.</strong></sup>
</div>
