# sample-mern-ci-cd

Sample for MERN CI + CD. Assumes the following:

 - You are using a public repository
 - You are using heroku for deployment
 - You have a MERN-like app with a `client` folder created with `create-react-app`
 - You want to use the lint config I've provided or are comfortable changing it
 - You have a `test` script in both your server and client `package.json`

## Setup

### Make install, start, and build a one-step command

1. Copy the `scripts` folder into the root of your project

2. Add the following to your `package.json` in the "scripts" object:

  ```js
  "start": "concurrently \"npm run server\" \"npm run client\"",
  "install": "cd client; yarn install; yarn build;",
  "server": "node server.js",
  "client": "node scripts/start-client.js",
  "build": "node scripts/build.js"
  ```
  _Note: `install` is actually run after the standard `npm install` so this runs both your client and server installs_

3. Test to make sure the server starts: `yarn server`

4. Test to make sure the client starts: `yarn client`

5. Test to make sure both can start: `yarn start`

6. Test to make sure you can run a client build from the root of the project: `yarn build`

7. Test to make sure that build worked and will be served up in production: `NODE_ENV=production node server.js`

### Add linting

1. Add the dev dependencies in this project:
  `yarn add -D eslint eslint-config-airbnb eslint-plugin-import eslint-plugin-jsx-a11y eslint-plugin-react babel-eslint`

2. Copy `.eslintignore` and `.eslintrc.json` into your project

3. You may want to run this to try to have it auto-fix the problems in your project:
  `./node_modules/.bin/eslint . --fix`

4. Add the following script to your `package.json` under "scripts":
  `"lint": "eslint ."`

5. Run `yarn lint` and fix problems until they're all gone. Odds are you'll have to change your `App.js` to be a pure component.

6. You may also want to add `// eslint-disable-next-line` in `server.js` right before the `console.log` or anywhere you want a `console.log` to make it into production.

### Add one-step lint + tests

1. Change the `test` script in your `package.json` to `test:server`:
  Before:
  ```
  scripts: {
    ...
    "test": "mocha"
    ...
  }
  ```
  After:
  ```
  scripts: {
    ...
    "test:server": "mocha"
    ...
  }
  ```

2. Add the following scripts to your root `package.json`:
  ```
  "test": "yarn lint; yarn test:server; CI=true yarn test:client",
  "test:client": "cd client; yarn test",
  ```
  _Note: CI=true ensures that the react-script tests are only run once_

3. Make sure all your test scripts work by running `yarn test`.

### Add a Procfile to ensure Heroku runs the right script

Copy `Procfile` into the root of your project

### Add configuration for Travis

Copy `.travis.yml` into the root of your project

### Set up Travis

*This step must be done by the owner of the repository*

1. Go to `travis-ci.org` and sign up using GitHub

2. Click the `+` in the upper left

3. If you don't see your repository, click "Sync Account"

4. Flip the switch by your repo

5. Push this to a branch and open a PR

### Require status checks + auto-deploy

1. In your GitHub Repo, navigate to "Settings > Branches"

2. Protect your master branch

3. Click "Protect this master branch" and "Require status checks to pass before merging" and make sure there is a travis-ci check visible

4. Go to Heroku and add a GitHub integration under "deploy" (make sure to require status checks)
