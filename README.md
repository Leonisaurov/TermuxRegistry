# TermuxRegistry

A Julia registry for the Termux/Android port, for packages that need a change on a host whose
`HostPlatform()` reports `libc = "bionic"`.  Forks keep the **same UUID** as the General package,
so the resolver sees a higher version and prefers it — nothing else in an environment has to
change.

```
Pkg.Registry.add("https://github.com/Leonisaurov/TermuxRegistry")
```

| Package | Version | Why |
| --- | --- | --- |
| `JLLWrappers` | 1.8.1 | On a bionic host `select_platform` finds no wrapper (a different `libc` disqualifies the match) and the code only does `@debug`, so every JLL silently leaves its products undefined. The fork falls back to the wrapper for the same `arch` and `os`, preferring glibc. |

Deps and compat come from each package's own `Project.toml` inside the registered tree, so there
is no `Deps.toml`/`Compat.toml` to keep in sync here.
