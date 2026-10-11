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
| `JLLWrappers` | 1.8.2 | On a bionic host `select_platform` finds no wrapper (a different `libc` disqualifies the match) and the code only does `@debug`, so every JLL silently leaves its products undefined. The fork falls back to the wrapper for the same `arch` and `os`, preferring glibc. 1.8.1 is yanked: its fallback only ran in the `host_platform !== nothing` branch, which no ordinary JLL takes. |

`Deps.toml` and `Compat.toml` are generated from the fork's own `Project.toml`, **but the
notation is not the same**: a registry range is a literal `VersionSpec`, so `julia = "1.0.0"`
there means *exactly* 1.0.x and `Preferences = "1.2.1"` means *exactly* 1.2.1 - whereas in a
`Project.toml` those are caret ranges.  Measured: with the caret spelling, resolution failed with
`restricted by julia compatibility requirement` and silently fell back to General's 1.8.0.  The
registry spelling is `julia = "1"` and `Preferences = "1.2.1 - 1"`, which is what General itself
records.  They are not
optional: a registry entry with no `Deps.toml` resolves the package **without dependencies**, and
loading it then fails with `Package JLLWrappers does not have Preferences in its dependencies`
(measured) - the package's `Project.toml` inside the tree is not consulted for this.
