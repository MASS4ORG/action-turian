# Turian CLI action

Build, export and inspect [Turian](https://github.com/MASS4ORG/Turian) game projects in GitHub Actions. The action runs the
headless `turian-cli` from the `ghcr.io/mass4org/turian-cli` container, so it needs a Linux runner with Docker (every
`ubuntu-*` hosted runner has it).

## Usage

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v7

      - uses: MASS4ORG/action-turian@v1
        with:
          args: export .

      - uses: actions/upload-artifact@v7
        with:
          name: game
          path: Output/
```

`export` writes the packaged game to the project's `Output/` folder.

## Inputs

| Input               | Default  | Description                                                                         |
|---------------------|----------|-------------------------------------------------------------------------------------|
| `args`              | required | Arguments for `turian-cli`: `compile .`, `export .`, `brick restore --locked`, …    |
| `version`           | `latest` | Tag of `ghcr.io/mass4org/turian-cli` to run; pin it to the Turian release you use   |
| `image`             |          | Full image reference to run instead, e.g. one built earlier in the same job         |
| `working-directory` | `.`      | Folder, relative to the workspace, the CLI runs in                                  |

## Outputs

| Output    | Description                                                          |
|-----------|----------------------------------------------------------------------|
| `image`   | The image that ran, pinned by digest: pass it as `image` to rerun it |
| `version` | The Turian version of that image                                     |

The tag is resolved to a digest once per step and that digest runs; the job summary records it too. So `latest` is fine
for day-to-day builds: when a build has to be reproduced exactly, the summary says which CLI made it.

The workspace is mounted at the same path inside the container, so paths in `args` match the ones later steps see. The
CLI runs as the runner's user, so later steps can change and delete what it writes.
`GAYA_BRICKS`, when set in the job, is passed through; point it inside the workspace and cache it to reuse the brick
store between runs.

## Other commands

```yaml
      - uses: MASS4ORG/action-turian@v1
        with:
          args: screenshot . --out screenshot.png
```

Run `turian-cli --help` (or `args: --help`) for the full list: `compile`, `export`, `scene`, `screenshot`, `ui`, `oap`,
`brick`, `import`.

## License

[MIT](LICENSE.md)
