# Adding a Vue island while the Void dev server is running

An existing counter hydrates normally. Adding a second island to the same page produces a second button that stays at `Count 0` when clicked.

## Reproduce

1. Run `pnpm install --frozen-lockfile` and `pnpm dev`.
2. Open the printed URL and click the counter. It should show `Count 1`. Keep the server running and the tab open.
3. In a second terminal at the project root, run:

   ```sh
   cp components/Counter.vue components/Second.vue
   git apply add-island.patch
   ```

4. Click the new (second) button. It stays at `Count 0`. Reload the page and click it again. The first button works, while the second still stays at zero.
5. Stop and restart `pnpm dev`, reload the page, and click the second button. It now increments.

The patch adds one island import with the `load` strategy and one component to the template. Both buttons should respond as soon as the edited page appears.

## Versions and observations

Verified in Chromium with the pinned npm packages (Void and @void/vue 1.6.2) and with both Void packages built from [main at 76d1a8a6](https://github.com/voidzero-dev/void/commit/76d1a8a6db0ddb4a16d903ebe9673a923bf5b539).

After step 3, the HTML contains both `data-island` wrappers, but the dev server's `virtual:void-islands-client` module only lists `components/Counter`. Restarting the server adds `components/Second` to the module.

To repeat from the initial state, stop the server, run `git restore pages/index.island.vue`, and remove `components/Second.vue`.
