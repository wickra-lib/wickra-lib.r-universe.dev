# wickra-lib.r-universe.dev

The package registry for <https://wickra-lib.r-universe.dev>. `packages.json`
lists every R package r-universe should build from the Wickra repositories; each
entry names the package, the repository it lives in, and the subdirectory holding
its `DESCRIPTION`.

## What can be listed

r-universe builds from source in an isolated container, so a package qualifies
only if that build can succeed without anything from the developer's machine.
Every Wickra R binding wraps a Rust C ABI, which means its `configure` has to
obtain a header and a shared library on its own.

Two shapes exist, and only one of them works here:

- **Download-based `configure`** — fetches the prebuilt
  `<product>-c-<triple>.tar.gz` release asset matching the package's `Version:`
  and bundles it. Self-contained, so r-universe can build it.
- **Environment-variable `configure`** — reads `WKEX_INC` / `WKTERM_INC` and the
  matching `_LIB`, which a clean container does not set. Cannot build here.

`wickra-exchange` and `wickra-backtest` were listed once and removed because
their bindings had only the second shape at the time, so the build could never
have worked. `wickra-backtest` has since gained a download-based `configure` and
would qualify; it is not re-listed here because, like `wickraterminal`, it has no
release yet, and whether to carry it red is a separate decision from this one.
`wickra-exchange` still has no download path.

## Why an entry can be red

A download-based `configure` still needs the release it downloads from.
`wickraterminal` is listed and its build **fails until wickra-terminal cuts its
first release**, because the asset URL its `configure` builds returns 404 until
then. That is expected, and the entry is deliberately kept rather than removed:
the registration, the package name and the subdirectory are all correct, and the
build turns green on its own the day the release lands.

Removing it to make the dashboard green would undo the registration and hide the
real blocker, which is the release rather than anything in this file.
