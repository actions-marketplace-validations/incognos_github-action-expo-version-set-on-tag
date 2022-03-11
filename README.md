[![build-test](https://github.com/robertherber/github-action-bump-eas-buildnumber/actions/workflows/test.yml/badge.svg)](https://github.com/incognos/github-action-eas-buildnumber-by-tag/actions/workflows/test.yml)

# Usage

This action sets the version, versionCode and buildNumber in app.json for Expo projects to the semver tag (i.e. v1.2.3 be comes version: 1.2.3, versionCode: 1002003 and buildNumber: 1.2.3). In your workflow you probably want to commit/push the changes to the same tag after your build is successful.

Input:

- filepath - path to your app.json (optional, defaults to './app.json')
- platforms - Comma-separated list of platforms to bump (optional, defaults to 'ios,android')
- tag - the tag version number, i.e. 'v1.0.1'

Output:

- version - The bumped iOS buildnumber removes any non-numeric or non-'.'
- versioncode - The bumped Android versioncode, e.g. 1000001 or v2.4.6 == 2004006 (major * 1000000 + minor * 1000 + patch)
- buildnumber - The bumped iOS buildnumber removes any non-numeric or non-'.'

Limitations:

- This solely bumps the app.json and does not commit/push the change, which is probably something you want to do after the build is successful.
- You can commit the changes to the branch and then `git -f -a {tag}` to move the tag to the new commit
- Please note that it will only work for app.json and not app.config.js, then you'll have to roll your own.

# Contribution Guide

### Code in Main

> First, you'll need to have a reasonably modern version of `node` handy. This won't work with versions older than 9, for instance.

Install the dependencies  

```bash
npm install
```

Build the typescript and package it for distribution

```bash
npm run build && npm run package
```

Run the tests :heavy_check_mark:  

```bash
$ npm test

 PASS  ./index.test.js
  ✓ throws invalid number (3ms)
  ✓ wait 500 ms (504ms)
  ✓ test runs (95ms)

...
```

### Change action.yml

The action.yml contains defines the inputs and output for your action.

Update the action.yml with your name, description, inputs and outputs for your action.

See the [documentation](https://help.github.com/en/articles/metadata-syntax-for-github-actions)

### Change the Code

Most toolkit and CI/CD operations involve async operations so the action is run in an async function.

```javascript
import * as core from '@actions/core';
...

async function run() {
  try { 
      ...
  } 
  catch (error) {
    core.setFailed(error.message);
  }
}

run()
```

See the [toolkit documentation](https://github.com/actions/toolkit/blob/master/README.md#packages) for the various packages.

### Publish to a distribution branch

Actions are run from GitHub repos so we will checkin the packed dist folder.

Then run [ncc](https://github.com/zeit/ncc) and push the results:

```bash
npm run package
git add dist
git commit -a -m "prod dependencies"
git push origin releases/v1
```

Note: We recommend using the `--license` option for ncc, which will create a license file for all of the production node modules used in your project.

Your action is now published! :rocket:

See the [versioning documentation](https://github.com/actions/toolkit/blob/master/docs/action-versioning.md)

### Validate

You can now validate the action by referencing `./` in a workflow in your repo (see [test.yml](.github/workflows/test.yml))

```yaml
uses: ./
with:
  milliseconds: 1000
```

See the [actions tab](https://github.com/actions/typescript-action/actions) for runs of this action! :rocket:

### Usage

After testing you can [create a v1 tag](https://github.com/actions/toolkit/blob/master/docs/action-versioning.md) to reference the stable and latest V1 action
