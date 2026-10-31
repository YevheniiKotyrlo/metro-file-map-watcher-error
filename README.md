# metro-file-map-watcher-error

An error a `metro-file-map` watcher reports ends the Metro server: `Watcher` subscribes each backend with `onFileEvent` only, so the `'error'` its `AbstractWatcher.emitError` emits has no listener and Node throws it. This repository is a plain Metro 0.87.2 project with:

- `index.js`, which requires `watched/value.js`;
- `metro.config.js`, which turns Watchman off, so the watcher is Metro's own;
- `use-metro-main.js`, which puts the `metro-file-map` a built react/metro checkout holds in place of the installed one.

## Steps

Linux (here WSL 2), Node 22 or later, npm.

1. Clone and install:
   ```sh
   git clone https://github.com/YevheniiKotyrlo/metro-file-map-watcher-error
   cd metro-file-map-watcher-error
   npm install
   ```
2. Start the server and wait for `Welcome to Metro`:
   ```sh
   npx metro serve
   ```
3. In a second terminal, in the same folder, take every permission off the folder:
   ```sh
   chmod 000 watched
   ```

Expected: the watcher reports that it cannot read the folder, and the server goes on.

Seen: the server exits with code 1, `Error: EACCES: permission denied, lstat '…/watched/watched'`, `Emitted 'error' event at: FallbackWatcher.emitError`.

## On react/metro main

Between steps 1 and 2, build a checkout of react/metro and put its `metro-file-map` in place:

```sh
git clone --depth 1 https://github.com/react/metro ../metro
(cd ../metro && yarn install --frozen-lockfile && node scripts/build.js)
node use-metro-main.js ../metro
```
