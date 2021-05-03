---
date: "2020-09-27T00:00:00Z"
title: Connect Jasmine, Karma, and Webpack to VS Code
---

If you'd like to learn how to run *and debug* [Jasmine](https://jasmine.github.io/2.0/introduction) tests executed using the [Karma](https://karma-runner.github.io/latest/index.html) test runner, right from [VS Code](https://code.visualstudio.com/), you've come to the right place.

Check out the repository referenced in this post at: [mikfreedman/jasmine-webpack-vscode](https://github.com/mikfreedman/jasmine-webpack-vscode)


## Installation 

### Install VSCode

```bash
brew cask install visual-studio-code
```

### Install [Angular/Karma Test Explorer for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=raagh.angular-karma-test-explorer)

### Install Node Packages

```bash
npm install
```

## Test For Great Success
Use the "Test Explorer" in VS Code to run / debug tests

Break right into your code, with variable names and everything!

<img src="{{site.baseurl}}/assets/images/results.png">
 
## How it works
Using the *Angular/Karma Test Explorer* extension, you can attach to the Chrome instance that's running your Karma tests. The following configuration makes this work: [karma.conf.js](https://github.com/mikfreedman/jasmine-webpack-vscode/blob/master/karma.conf.js), [.vscode/settings.json](https://github.com/mikfreedman/jasmine-webpack-vscode/blob/master/.vscode/settings.json). The repo contains step by step diffs in its git log, I've detailed some key points below.

### Attaching the debugger
The *Angular/Karma Test Explorer* expects you to setup karma to execute chrome to listen for debugger connections on port **9222**. Here's what the extension's default **launch.json** looks like:

```json
    "angularKarmaTestExplorer.debuggerConfiguration": {
        "name": "Debug tests",
        "type": "chrome",
        "request": "attach",
        "port": 9222,
        "sourceMaps": true,
        "webRoot": "${workspaceRoot}",
        "sourceMapPathOverrides": {
            "webpack:/*": "${webRoot}/*",
            "/./*": "${webRoot}/*",
            "/src/*": "${webRoot}/*",
            "/*": "*",
            "/./~/*": "${webRoot}/node_modules/*"
        }
    }
```

So we need to setup our [karma.config.js](https://github.com/mikfreedman/jasmine-webpack-vscode/blob/master/karma.conf.js) to allow for this connection:

```diff
-      browsers: ['ChromeHeadless'/*,'PhantomJS','Firefox','Edge','ChromeCanary','Opera','IE','Safari'*/],
+      browsers: ['ChromeHeadlessDebugging'/*,'PhantomJS','Firefox','Edge','ChromeCanary','Opera','IE','Safari'*/],
+      customLaunchers: {
+        ChromeHeadlessDebugging: {
+          base: 'ChromeHeadless',
+          flags: [
+            '--remote-debugging-port=9222'
+          ]
+        }
+      },
```

Confirm this works by looking at the extension logs and seeing something like this:

```
[3:24:25 PM] INFO: Listening to AngularReporter events on port 9222
[3:24:33 PM] INFO: Test loading completed
```

### Mapping Source
Get webpack to emit inline source maps in [karma.conf.js](karma.conf.js). Also ask it nicely to not minimize the generated code so that variable names match.

```diff
       webpack: {
+        devtool: "inline-source-map",
+        optimization: {
+          minimize: false
+        },
```

## Kill stray stuff

Keep an eye out for errant Chrome instances that gum up the works and generally foster weird behavior.

```bash
ps -ef | grep 9222 | cut -f 5 -d ' ' | xargs kill
```

 [Check out what this does](https://explainshell.com/explain?cmd=ps+-ef+%7C+grep+9222+%7C+cut+-f+5+-d+%27+%27++%7C+xargs+kill)

