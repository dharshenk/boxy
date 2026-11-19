# Boxy

A tiny Linux sandbox runner. Boxy runs a command in an isolated, resource-limited environment so you can safely execute untrusted code, such as scripts written by an LLM or a stranger, without risking your machine.

```
boxy run --mem 256M --cpus 1 --timeout 5s --no-net -- python script.py
```

## Why

Anyone running AI-generated or third-party code needs isolation, but the options are heavy (full Docker setups, VMs) or hosted (paid sandbox APIs). Boxy aims to be a single small binary that does one job: run this command in a cage, then clean up.

It is also a learning project. Building it teaches what a "container" really is: a few Linux kernel features combined.

## Features (planned)

- **Process isolation**: own PID, mount, UTS, IPC and user namespaces; the command sees only itself
- **Filesystem isolation**: runs in a minimal rootfs via `pivot_root`; optional read-only bind mounts and a writable scratch dir
- **Resource limits** (cgroups v2): memory, CPU, and max processes, so a fork bomb or memory hog is contained
- **Time limit**: the whole process tree is killed when the timeout expires
- **Network off by default**: own network namespace; opt in with `--net`
- **Syscall filtering**: seccomp allowlist plus dropped capabilities
- **Clean exit reporting**: exit code, stdout/stderr, wall time, peak memory, and whether a limit was hit (OOM, timeout)
- **Optional HTTP API**: `POST /run` for use from agents and other programs

## Milestones

1. `boxy run -- /bin/sh`: new PID and mount namespaces; `ps` shows PID 1
2. Minimal rootfs with `pivot_root`; the host filesystem is invisible
3. cgroups v2 limits for memory, CPU and pids; a fork bomb dies harmlessly
4. Timeout and tree-kill; structured result output (JSON)
5. Network namespace, off by default
6. Capability dropping and seccomp filter
7. HTTP API and a small test suite of "escape attempts"

## Non-goals

Boxy is not a Docker replacement: no image registry, no orchestration, no daemon required. It also does not claim to be a hardened security boundary against a determined attacker; for that, use a microVM (e.g. Firecracker) or gVisor.

## Requirements

Linux with cgroups v2 and user namespaces enabled (works in WSL2).

## Status

Idea stage. Language: Go or Rust (TBD).
