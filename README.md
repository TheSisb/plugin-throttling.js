# plugin-throttling.js

> Octokit plugin for GitHub’s recommended request throttling

[![npm](https://img.shields.io/npm/v/@octokit/plugin-throttling.svg)](https://www.npmjs.com/package/@octokit/plugin-throttling)
[![Build Status](https://travis-ci.org/octokit/plugin-throttling.js.svg?branch=beta)](https://travis-ci.org/octokit/plugin-throttling.js)
[![Coverage Status](https://img.shields.io/coveralls/github/octokit/plugin-throttling.js/beta.svg)](https://coveralls.io/github/octokit/plugin-throttling.js)
[![Greenkeeper](https://badges.greenkeeper.io/octokit/plugin-throttling.js.svg)](https://greenkeeper.io/)

Implements all [recommended best practises](https://developer.github.com/v3/guides/best-practices-for-integrators/) to prevent hitting abuse rate limits.

## Usage

The code below creates a "Hello, world!" issue on every repository in a given organization. Without the throttling plugin it would send many requests in parallel and would hit rate limits very quickly. But the `@octokit/plugin-throttling` makes sure that no requests using the same authentication token are throttled correctly.

```js
const Octokit = require('@ocotkit/rest')
  .plugin(require('@octokit/plugin-throttling'))

const octokit = new Octokit()
octokit.authenticate({
  type: 'token',
  token: process.env.TOKEN
})

async function createIssueOnAllRepos (org) {
  const repos = await octokit.paginate(octokit.repos.listForOrg.endpoint({ org }))
  return Promise.all(repos.forEach(({ name } => {
    octokit.issues.create({
      owner,
      repo: name,
      title: 'Hello, world!'
    })
  })))
}
```

Handle events

```js
octokit.throttle.on('rate-limit', (retryAfter) => console.warn(`Rate-limit hit, retrying after ${retryAfter}s`))
octokit.throttle.on('abuse-limit', (retryAfter) => console.warn(`Abuse-limit hit, retrying after ${retryAfter}s`))
```

## LICENSE

[MIT](LICENSE)
