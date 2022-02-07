# Renovate bot presets

This repo contain a set of [Renovate Bot](https://www.whitesourcesoftware.com/free-developer-tools/renovate/) presets that modules in this organization can depend on.

The purpose of these presets is to be able to optimize the process of keeping dependencies up to date accross all modules in this organization while minimizing the amount of releases of each module in this organization caused by automatically bumping dependencies.

As an example lets say we have a set of modules using Renovate Bot and Semantic Release structured like so:

```sh
|--/@eik/module-a
   |--/3rd-party-dependency
   |--/@eik/module-b
      |--/3rd-party-dependency
```

`@eik/module-a` and `@eik/module-b` depend on the same `3rd-party-dependency`.

The `@eik/module-a` is a publicly facing module. Its a module which others will use as a dependency. `@eik/module-b` is __not__ a publicly facing module. Its an internal module which contain code only of interest of other modules in our organization and it should never be used by externals as a dependency.

If we where to use the default configuration from Renovate Bot the following would happen when we have Semantic Release in our setup and there is a new version of the `3rd-party-dependency`: `@eik/module-a` would get a PR with an update of the `3rd-party-dependency` and when merged a new release of `@eik/module-a` would be cut. Meanwhile `@eik/module-b` would also get a PR with an update of the `3rd-party-dependency` and when merged a new release of `@eik/module-b` would also be cut. 

This release of `@eik/module-b` would again create a new PR in `@eik/module-a` and cause a new release of `@eik/module-a` when merged. 

In other words; Due to both `@eik/module-a` and `@eik/module-b` depending on `3rd-party-dependency` an update of `3rd-party-dependency` will cause two releases of `@eik/module-a`. Which is not optimal.

## Module categorisation

To deal with problems such as described in the introduction above we separate modules into two categories, top modules and sub modules, where each category have slightly different grouping and dependency strategies.

In short; sub modules recieve and merge dependencies as they arrive. Top modules recieve and merge dependencies on a less frequent schedule. This more or less removes the above outlined problem.

### Top modules

Top modules is publicly facing modules. This is modules which others will use as a dependency. 

Top modules has its dependencies updated once a week.

To use the preset for top modules in a module add the following config to `renovate.json`:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "github>eik-lib/renovate-presets:top-level-module"
  ]
}
```

### Sub modules

Sub modules is __not__ publicly facing modules. They are internal modules which contain code only of interest of top modules and hey should never be used by externals as a dependency.

Sub modules has its dependencies updated as they are published.

To use the preset for sub modules in a module add the following config to `renovate.json`:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "github>eik-lib/renovate-presets:sub-level-module"
  ]
}
```

## Dev dependencies

Dev dependencies is updated once a month. Both top and sub modules have the same dev dependency preset. 

## License

See license file.
