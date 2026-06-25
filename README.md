# RunToSolveJuliaRegistry

Company Julia package registry for [RunToSolve](https://github.com/runtosolve) packages. Add it once alongside the standard General registry so that `Pkg.add` can resolve RunToSolve dependencies automatically.

---

## Setup the registry

### Add the registry

Registries are **global** in Julia — they are stored in the Julia depot (`~/.julia/registries/`) and shared across all projects on the machine. Run this once per machine (or per Julia depot):

```julia
using Pkg
Pkg.Registry.add(RegistrySpec(url="https://github.com/runtosolve/RunToSolveJuliaRegistry"))
```

Confirm it was added:

```julia
Pkg.Registry.status()   # should list RunToSolveJuliaRegistry alongside General
```

### Install LocalRegistry (developers only)

Package registration requires [LocalRegistry.jl](https://github.com/GunnarFarneback/LocalRegistry.jl). Install it once into your **global** Julia environment (not into the package being registered):

```julia
# Run outside any activated project, or explicitly target the global env:
using Pkg
Pkg.activate()          # switches to the global environment
Pkg.add("LocalRegistry")
Pkg.activate(".")       # switch back to your package
```

---

## Using the Registry

### Install a package

After the registry is added, install any RunToSolve package the usual way:

```julia
Pkg.add("CUFSM")
```

To pin a specific version:

```julia
Pkg.add(name="CUFSM", version="0.4.1")
```

### Update packages

```julia
Pkg.Registry.update()   # pull latest registry metadata
Pkg.update()            # upgrade all packages, or pass a name to update one
```

---

## Registering Packages (developers only)

### Register a new package

1. The package must already have a GitHub repository under the `runtosolve` org with a `Project.toml` containing a `name`, `uuid`, and `version`.
2. From a Julia session inside the package directory (environment activated):

```julia
using LocalRegistry
register("RunToSolveJuliaRegistry")
```

`register` will write the new package entry into the registry and push the change automatically. Passing the registry name explicitly is required if you have more than one custom registry; if `RunToSolveJuliaRegistry` is your only custom registry, `register()` works as well.

### Release a new version of an existing package

**Step 1 — Bump the version in `Project.toml`**

Edit `version` following [SemVer](https://semver.org/):

```toml
version = "0.2.3"   # was 0.2.2
```

Commit and push:

```bash
git add Project.toml
git commit -m "bump version to 0.2.3"
git push
```

**Step 2 — Tag the release (recommended)**

```bash
git tag v0.2.3
git push origin v0.2.3
```

Alternatively, create a GitHub Release through the web UI.

**Step 3 — Register the new version**

From a Julia session inside the package directory (environment activated):

```julia
using LocalRegistry
register("RunToSolveJuliaRegistry")
```

`register` will read the new version from `Project.toml`, resolve the git tree hash, update the registry entry, and push the change. Passing the registry name explicitly is required if you have more than one custom registry; if `RunToSolveJuliaRegistry` is your only custom registry, `register()` works as well.

**Step 4 — Verify**

```julia
Pkg.Registry.update()
Pkg.status("CUFSM")   # replace with your package name — should show the new version
```
