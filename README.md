# vbump

```
usage: vbump options

This commits a version bump and tags it git, then published to npm.

OPTIONS:
   -v  [required] Target version to bump.
   -t  Tag only. Do not git commit.
   -h  Show this message.
   -p  Do not publish to npm.
   -m  Commit message you wish to use.
   -d  Dry-run. No nothing but echo commands.
   -P, --path FILE
       Bump one package in a monorepo. FILE is that package's package.json.
       The version defaults to its "version", the tag is "<name>@<version>",
       only its directory is staged and publish runs inside it (with pnpm
       when the repository root has a pnpm-workspace.yaml, else npm).
```

## Monorepos: one tag per package

In a pnpm workspace, point `--path` at the `package.json` of the package you
are releasing. vbump reads its `name` and `version`, tags
`<name>@<version>` (the changesets / pnpm convention), stages only that
package's directory, and publishes from inside it with `pnpm publish` so
`workspace:` ranges are rewritten to real versions.

```
.
├── pnpm-workspace.yaml
└── packages
    ├── core/package.json          # "@autocache/core"       "1.2.3"
    └── waybackify/package.json    # "@autocache/waybackify" "0.5.0"
```

After editing `packages/waybackify/package.json` to `0.5.0`:

```sh
vbump --path packages/waybackify/package.json
```

runs

```
git add packages/waybackify
git commit -m "[dist] Version bump. @autocache/waybackify@0.5.0"
git tag -a @autocache/waybackify@0.5.0 -m "Version @autocache/waybackify@0.5.0"
git push --tags
git push origin master
cd packages/waybackify
pnpm publish
```

`-v`, `-m`, `-t`, `-p` and `-d` all combine with `--path`; `-v` overrides the
version read from the file. Without `--path` nothing changes: the tag is the
bare version, `git add .` stages everything and `npm publish` runs at the root.

##### Author: [Charlie Robbins](https://github.com/indexzero)
##### License: MIT
