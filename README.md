# @ygorluiz/renovate-config

My [shareable config](https://renovatebot.com/docs/config-presets/) for [Renovate](https://renovatebot.com)

[![npm version][npm-image]][npm-url]
[![build status][ci-image]][ci-url]
[![dependency status][deps-image]][deps-url]
![License][license]

## Setup

Enable Renovate in your repo and just `extends` in `renovate.json`.

```js
{
  "extends": ["github>ygorluiz/renovate-config"] // or ["github>ygorluiz/renovate-config:anytime"]
}
```

Note: You don't have to do `npm i -D @ygorluiz/renovate-config`.
Renovate fetches it from this GitHub repo automatically.

## Presets

### `github>ygorluiz/renovate-config` (`default`)

#### General

- Ignore `node_modules`, `bower_components`, and various test/tests directories
- Apply label `renovate` to PRs
- Limit to maximum 10 concurrent Renovate PRs at any time
- Wait until branch tests have passed or failed before creating the PR
- If semantic commits detected, use semantic commit type `fix` for deps and peerDeps, `chore` for all others
- Use Asia/Tokyo timezone
- Group [preset monorepo packages](https://renovatebot.com/docs/presets-monorepo/) together

#### for npm

- Automerge patch upgrades if they pass tests
- Make no updates to branches when not scheduled
- Separate major, minor and patch releases of dependencies into individual branches/PRs
- Set a status check to warn when upgrades < 24 hours old might get unpublished
- Run `npm dedupe` after package-lock.json updates
- Disable major upgrade of `@types/node`
- Run following schedule: after 9pm and before 9am
- Upgrade semver ranges to latest version even if latest version satisfies existing range.
- Group ESLint, ESLint configs, ESLint plugins and Prettier together
- Automerge minor updates of widely used libraries like `mocha` in devDeps

#### for lock file maintenance

- Run following schedule: before 3am on the first day of the month

#### for Docker digests in CirleCI config.yml

- Pin docker images with sha256 digest
- Automerge with push the new commit directly to base branch (w/o PR)
- Use `ci(docker):` as semantic commit type
- Run following schedule: before 9am on Friday
- Group all versions of Node.js images (`node` and `circleci/node`)

#### for `engines` field in `package.json`

- disabled

```json
{
  "extends": [
    ":ignoreModulesAndTests",
    ":label(renovate)",
    ":prConcurrentLimit10",
    ":prNotPending",
    ":timezone(Asia/Tokyo)",
    "group:monorepos"
  ],
  "npm": {
    "extends": [
      ":automergePatch",
      ":noUnscheduledUpdates",
      ":separatePatchReleases",
      "npm:unpublishSafe",
      "helpers:disableTypesNodeMajor",
      "local>ygorluiz/renovate-config:semanticPrefixFixDepsPeerChoreOthers"
    ],
    "schedule": ["after 9pm", "before 9am"],
    "rangeStrategy": "bump",
    "postUpdateOptions": ["npmDedupe"],
    "lockFileMaintenance": {
      "enabled": true,
      "schedule": ["before 3am on the first day of the month"]
    },
    "packageRules": [
      {
        "groupName": "ESLint and Prettier",
        "matchPackageNames": ["eslint", "prettier"],
        "matchPackagePatterns": ["^eslint-config-", "^eslint-plugin-"]
      },
      {
        "description": "automerge minor updates of widely used libraries in devDeps",
        "matchUpdateTypes": ["minor"],
        "matchDepTypes": ["devDependencies"],
        "automerge": true,
        "matchPackageNames": [
          "glob",
          "mocha",
          "npm-run-all",
          "power-assert",
          "rimraf",
          "sinon"
        ]
      },
      {
        "description": "disable package.json > engines update",
        "matchDepTypes": ["engines"],
        "enabled": false
      }
    ]
  },
  "circleci": {
    "enabled": true,
    "automerge": true,
    "automergeType": "branch",
    "schedule": ["before 9am on Friday"],
    "semanticCommitScope": "docker",
    "semanticCommitType": "ci",
    "pinDigests": true,
    "packageRules": [
      {
        "groupName": "Node Docker digests in CircleCI",
        "matchPackageNames": ["circleci/node", "node"]
      }
    ]
  }
}
```

### `github>ygorluiz/renovate-config:anytime`

- Run Renovate **_at any time_**

```json
{
  "extends": ["local>ygorluiz/renovate-config"],
  "npm": {
    "schedule": "at any time"
  },
  "lockFileMaintenance": {
    "schedule": "at any time"
  },
  "circleci": {
    "schedule": "at any time"
  }
}
```

### `github>ygorluiz/renovate-config:semanticPrefixFixDepsPeerChoreOthers`

If semantic commits detected, use semantic commit type `fix` for `dependencies` and `peerDependencies`, `chore` for all others

```json
"semanticPrefixFixDepsPeerChoreOthers": {
  "packageRules": [
    {
      "matchPackagePatterns": [
        "*"
      ],
      "semanticCommitType": "chore"
    },
    {
      "matchDepTypes": [
        "dependencies",
        "peerDependencies"
      ],
      "semanticCommitType": "fix"
    }
  ]
}
```

## References

- [Renovate Docs](https://renovatebot.com/docs/)
- [Configuration Options \| Renovate Docs](https://renovatebot.com/docs/configuration-options/)
- [Default Presets \| Renovate Docs](https://renovatebot.com/docs/presets-default/)
- [Other shareable configs in GitHub](https://github.com/search?o=desc&q=%22renovate-config%22&s=stars&type=Repositories&utf8=%E2%9C%93)
- [RunKit \+ npm: later](https://npm.runkit.com/later): [later](https://www.npmjs.com/package/later) is a parser that used to parse `schedule` in renovate

## License

MIT License: Teppei Sato &lt;ygorluiz@gmail.com&gt;

[npm-image]: https://img.shields.io/npm/v/@ygorluiz/renovate-config.svg
[npm-url]: https://npmjs.org/package/@ygorluiz/renovate-config
[npm-downloads-image]: https://img.shields.io/npm/dm/@ygorluiz/renovate-config.svg
[ci-image]: https://github.com/ygorluiz/renovate-config/workflows/ci/badge.svg
[ci-url]: https://github.com/ygorluiz/renovate-config/actions?query=workflow%3Aci
[deps-image]: https://img.shields.io/david/ygorluiz/renovate-config.svg
[deps-url]: https://david-dm.org/ygorluiz/renovate-config
[node-version]: https://img.shields.io/badge/Node.js%20support-v6,v8,v9-brightgreen.svg
[coverage-image]: https://img.shields.io/coveralls/ygorluiz/renovate-config/master.svg
[coverage-url]: https://coveralls.io/github/ygorluiz/renovate-config?branch=master
[license]: https://img.shields.io/npm/l/@ygorluiz/renovate-config.svg
