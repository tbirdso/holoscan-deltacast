# holoscan-deltacast package

This directory defines the binary packages for `holoscan-deltacast`.

## Usage

```bash
./holohub package holoscan-deltacast --pkg-generator DEB
./holohub package holoscan-deltacast --pkg-generator WHEEL
./holohub package holoscan-deltacast --pkg-generator DEB,WHEEL
```

## metadata.json

`metadata.json` registers this package with the holohub CLI. Two fields matter:

- **`package` key** — marks this directory as a HoloHub *package* project. The CLI
  discovers it via the recursive `HOLOHUB_SEARCH_PATH` scan from the module root,
  which makes it appear under the `PACKAGES` section of `./holohub list`.
- **`package.dockerfile`** — path (relative to the module root) to the container
  image used by `./holohub package` for container-based package builds.

Looking for Python packaging? Review the project [pyproject.toml](../../pyproject.toml)
for configuration.
