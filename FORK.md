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

All our changes are on `accuport-custom-script` branch. If you want to update to
the latest CRA you need to do the following:

`git fetch upstream` and then rebase master on our branch as follow:

Then you need identify the latest commit SHA that has been published. This is to
ensure that the code you're about to rebase has been versionned and published.
Even if we are creating our own package of react-scripts, other packages might
still be a dependency.

To do so, use `git log upstream/master` and identify the latest commit attached
to a tag or you can visit https://github.com/facebook/create-react-app/releases
and grab the commit SHA of the latest release.

```
$ git checkout accuport-custom-script && git pull origin accuport-custom-script
$ git rebase COMMIT_SHA
// fix potential conflicts
```

Bump the version accordingly to follow more or less Facebook versions:
`cd packages/react-scripts` and edit `package.json`.

You can now try locally your latest script (with link) or publish a new version directly on
NPM.

```
$ npm publish
// In another folder - create a new app based on this script:
$ npx create-react-app sandbox-accuport-based-cra --typescript --scripts-version @s19/accuport-react-scripts
```

Instead of creating a sandbox, you can try directly to bump
`@s19/accuport-react-scripts` in the existing projects like the Portal. Make
sure you can compile everything and the app is running.

Once it's all good you can push your updated branch:

```
$ git push origin accuport-custom-script -f
```

⚠️ ⚠️If you are unsure, please try to rebase on another new branch first before
to force push on `accuport-custom-script` branch!

Please make sure you use rebase and not a merge as our changes should always be
on the top.
([doc](https://stefanbauer.me/articles/how-to-keep-your-git-fork-up-to-date)).
