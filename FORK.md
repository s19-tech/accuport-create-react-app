# Accuport CRA Fork

# Context

In June 2017, the first dashboard Accuport project, `ap-webclient` [started
based on](https://github.com/s19-tech/ap-webclient/commit/167ecbd) the first
version of Create React App (CRA). Because of custom requirements around CSS and
how to build them, the project was [ejected 5 days
later](https://github.com/s19-tech/ap-webclient/commit/a15da60). Since then few
minor patches have been added to the configuration without maintaining
dependencies for build (like Webpack). Unfortunately, the second project
([Portal](https://github.com/s19-tech/ct-webclient)) was [started on
top](https://github.com/s19-tech/ct-webclient/commit/65ec9dd) of the current
Dashboard configuration at the time... Because the community around the CRA
project is active, it seems better to rely on it as much as possible to keep-up
with updates on the configuration. Thus this fork.

# Now

Instead of ejecting from the original create-react-app, we decided to use a fork
version of the script that handle our specific configuration for the Portal
first and possibly the Dashboard later.

We need it at the moment for two things:

- Use PostCSS loader to mimic more Sass functionality
- Use a file loader for Graphql

Please try to stick as close as possible from the original version of CRA. This
fork is there only to maintain compatibility with existing project but should be
avoided with new ones.

## Update and keep upstream up-to-date

### Requirements

Since this is a fork, the first thing you need is to have an upstream pointing
to the original project. Make sure you've got the following configuration `git remote -v`

```
~/s19-accuport/accuport-create-react-app $ git remote -v
origin  git@github.com:s19-tech/accuport-create-react-app.git (fetch)
origin  git@github.com:s19-tech/accuport-create-react-app.git (push)
upstream        git@github.com:facebook/create-react-app.git (fetch)
upstream        git@github.com:facebook/create-react-app.git (push)
```

In case you don't, you can add upstream with the following command: `git remote add upstream git@github.com:facebook/create-react-app.git`.

### Rebase latest master

All our changes are on branches prefixed with `accuport-` followed by a version.
If you want to update to the latest CRA you need to do the following:

```
$ git fetch --all
$ git checkout master
$ git pull upstream master
```

**Then you need identify the latest commit SHA that has been published from
Facebook**. This is to ensure that the code you're about to rebase has been
versioned and published (and ultimately tested). Even if we are creating our own
package of react-scripts, other packages might still be a dependency.

To do so, use `git log upstream/master` and identify the latest commit attached
to a tag or you can visit https://github.com/facebook/create-react-app/releases
and grab the commit SHA (`COMMIT_SHA` used later) of the latest release.

Finally, identify the latest published version from Accuport (this fork) and
checkout this branch. For example, if the latest version published is `v3.1.7`
we'll run the following command:

```
$ git fetch origin accuport-v3.1.7:accuport-v3.1.7 // if you don't have it
locally
$ git checkout accuport-v3.1.7 && git pull origin accuport-v3.1.7
$ git checkout -b accuport-v3.2.0 // Create a new branch version
$ git rebase COMMIT_SHA
// fix potential conflicts + breaking changes from dependencies.
```

Fix each commit separately.

### Test the latest version

In order to mimic as much as possible the publication of the package and because
the `npm link` command locally does not correctly apply `peerDependencies`, we
recommend to use the `npm pack` command that will create a tarball identical to
the one published by CI.

```
$ cd packages/react-scripts
$ npm pack
```

This should generate a tarball like:
`packages/react-scripts/s19-accuport-react-scripts-3.2.0.tgz`.

You can now try this package directly with the Portal by editing `package.json`
and point to this local tarball. For example:

```
// ct-webclient/package.json
...
  "dependencies": {
        "@s19/accuport-react-scripts": "~/accuport-create-react-app/packages/react-scripts/s19-accuport-react-scripts-3.3.0.tgz",
...
```

Run the Portal app and tests. Make sure the app is running without any error.
If anything goes wrong, you should amend / rebase our commits on top to make
them pass.

**For extra security**, you can also publish a release candidate at that stage
by updating the version in `packages/react-scripts/package.json` with `-rc.1`
and checkout a new branch. In our example:

`git checkout -b accuport-v3.2.0-rc.1` and then push. The CI will package and
publish this release candidate.

Finally, once everything looks good and you're confident this version can be
used, you can push your changes.

```
$ git push origin accuport-v3.2.0
```

> Before version 3.3.0, we used to bump manually the version and we were
> out-of-sync with main upstream because we lack of proper way to test the
> packages.

⚠️ ⚠️Please test it thoroughly to avoid that and use RC if not sure.

Please make sure you use rebase and not a merge as our changes should always be
on the top.
([doc](https://stefanbauer.me/articles/how-to-keep-your-git-fork-up-to-date)).

### Test without the Portal

```
// In another folder - create a new app based on this script:
$ npx create-react-app sandbox-accuport-based-cra --typescript --scripts-version @s19/accuport-react-scripts
```
