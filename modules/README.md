# Your own modules

Modules that only this app uses can live here, in a folder each. They need no repository of their own. To build one, list this folder in `mirante.json`:

```json
{
  "name": "app_modules",
  "local": "modules",
  "folders": ["acmetickets"]
}
```

`local` is a folder of this repository, relative to its top. The About dialog shows this repository and its commit for these modules, and CI checks their includes, as for every module.

[Writing a module](https://github.com/rrgmc/mirante-core/blob/master/docs/writing-a-module.md) in the core says how to write one, and [`mirante-modules-samples`](https://github.com/rrgmc/mirante-modules-samples) has small examples.
