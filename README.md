# Heroku Buildpack for GitHub Packages Authentication

This is a Heroku buildpack that enables authenticated GitHub Packages operations
within a Heroku dyno.

It detects an `NPM_AUTH_TOKEN` environment variable and creates a `.npmrc` file.

It's cloned from the [npm buildpack](https://github.com/nice-registry/npm-buildpack) by nice-registry.

## Usage

Get a user token from GitHub with the packages:read and repo permissions. (Note: I don't know if the repo permissions are entirely required.)

Save the token in your Heroku app config:

```sh
heroku config:set GITHUB_AUTH_TOKEN=YOUR_TOKEN_HERE
```

Configure your app to use this buildpack:

```sh
heroku buildpacks:add --index 1 https://github.com/tdukart/github-packages-buildpack
```

The next time you push your app to Heroku, this buildpack will create a
`.npmrc` file containing your npm token in the base directory of the app:

```sh
heroku run bash
cat .npmrc
//registry.npmjs.org/:_authToken=00000000-0000-0000-0000-000000000000
```

Now you can perform authenticated npm operations on the dyno, including
`npm publish`!

## Tips

Tip: If you ever change the token, you'll need to redeploy the app to
ensure a new .netrc file is created:

```sh
heroku config:set GITHUB_AUTH_TOKEN=NEW_TOKEN
git commit --allow-empty -m "update dat GitHub token"
git push heroku master
```

Tip: Heroku's node buildpack will install `dependencies` from package.json
by default. If your app needs `devDependencies` to be installed too,
set the following in your app environment:

```sh
heroku config:set NPM_CONFIG_PRODUCTION=false
```
