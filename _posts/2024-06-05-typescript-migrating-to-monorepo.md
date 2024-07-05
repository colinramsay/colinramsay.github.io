---
layout: post
title: Migrating a TypeScript repository to a monorepo
published: false
---

## Rationale

Our existing web app is getting a sibling: a mobile app. There's 

- Move everything into a packages/web directory using `git mv`

```
for file in .* *; do
    if [ "$file" != "." ] && [ "$file" != ".." ] && [ "$file" != "packages" ] && [ -e "$file" ]; then
        git mv "$file" ./packages/web
    fi
done
```


- Add package.json at root

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

References & inspiration:

* https://medium.com/edgybees-blog/how-to-move-from-an-existing-repository-to-a-monorepo-using-npm-7-workspaces-27012a100269
* https://dev.to/limal/simplify-your-monorepo-with-npm-7-workspaces-5gmj
