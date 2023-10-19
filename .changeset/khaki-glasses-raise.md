---
'astro': minor
---

## Integration Hooks to add Middleware

It's now possible in Astro for an integration to add middleware on behalf of the user. Previously when a 3rd party wanted to provide middleware the user would need to create a `src/middleware.ts` file themselves. Now adding 3rd party middleware is as easy as adding a new integration.

For integration authors, there is a new `addMiddleware` function in the `astro:config:setup` hook. This function allows you to specify a middleware module and the order in which it should be applied:

__@my-package/middleware.js__

```js
import { defineMiddleware } from 'astro:middleware';

export const onRequest = defineMiddleware(async (context, request) => {
  if(context.url.pathname === '/some-test-path') {
    return Response.json({
      ok: true
    });
  }
});
```

__@my-package/integration.js__

```js
export function myIntegration() {
  return {
    name: 'my-integration',
    hooks: {
      'astro:config:setup': ({ addMiddleware }) => {
        addMiddleware({
          entrypoint: '@my-package/middleware',
          order: 'pre'
        });
      }
    }
  };
}
```
