---
layout: post
title: Migrating a TypeScript repository to a monorepo
published: false
---

## Rationale

Our existing web app is getting a sibling: a mobile app. There's 

### 1. Move everything into a packages/web directory using `git mv`

All of the "packages" making up the monorepo need to move to their own subdirectories. We only have one package right now, which I've called "web". You can use something like this:

```
for file in .* *; do
    if [ "$file" != "." ] && [ "$file" != ".." ] && [ "$file" != "packages" ] && [ -e "$file" ]; then
        git mv "$file" ./packages/web
    fi
done
```

### 2. Add package.json at root

This tells npm that we want our packages to be managed from the top level of the monorepo. The packages are defined as [NPM workspaces](https://docs.npmjs.com/cli/v7/using-npm/workspaces?v=true).

```
{
  "name": "@colinramsay/monorepo",
  "version": "1.0.0",
  "scripts": {
  },
  "workspaces": [
    "./packages/*"
  ]
}
```

### 3. Add a root tsconfig.json

Copy from packages/web to root, removing the `baseUrl` and `paths` keys. Probably remove anything else that's specific to your web app.

### 4. Add references in root tsconfig.json:

```
  "references": [
    { "path": "./packages/web" }
  ]

```

### 5. Amend the tsconfig.json of the workspace

The goal here is to remove anything generic to all of your apps, and inherit it from the root. There may be lots of extra things in here - it depends on the exact requirements of your web app.

```
{
  "extends": "../../tsconfig.json",
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "~/*": ["./app/*"]
    }
  }
}

```

### 6. (Maybe) Fix your workflows

In our case, we use GitHub Actions for CI/CD. Your workflow will now be flipping out because it doesn't know where to find anything. Let's fix that.

#### 6.1 Change your trigger

????


```
-on: [push]
+on:
+  push:
+    paths:
+      - 'packages/web/**'
```

#### 6.2 Change your commands

Any `npm run` command will operate at the root of the monorepo by default, so if you want to run it within the "web" workspace you can either pass `--workspace` or you could `cd` into "packages/web" first.

```
-run: npm run lint
+run: npm run lint --workspace=packages/web

#### 

### 7. (Maybe) Fix your deployment

Your current deployment is probably thinks your application is in the root of your repository, so you'll need to fix that. Depending on your hosting provider, this might be as simple as changing the [deployment root](https://docs.render.com/monorepo-support#root-directory).

## Monorepo-fication complete!




### 5. Add another workspace

Often a monorepo's packages will share code - in this case our web and new mobile packages will use code in a new common package. 


##


References & inspiration:

* https://medium.com/edgybees-blog/how-to-move-from-an-existing-repository-to-a-monorepo-using-npm-7-workspaces-27012a100269
* https://dev.to/limal/simplify-your-monorepo-with-npm-7-workspaces-5gmj
