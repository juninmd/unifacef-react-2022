## Sentry

Crie um arquivo em

```text
./src/plugins/sentry.plugin.ts
```

```ts
import { configureScope, init } from '@sentry/browser'
import { configs } from '../configs';

(() => {
  // Desativa o plugin localhost
  if (window.location.hostname === 'localhost' ||
    window.location.hostname === '127.0.0.1') {
    return;
  }

  init({ dsn: configs.sentry });

  configureScope(scope => {
  })
})();
```

E depois importe em

```text
./src/index.tsx
```

```ts
import './plugins/sentry.plugin';
```

Validar env no arquivo

```text
.env
```

Contendo as informaçãoes do seu Sentry, por exemplo:

```text
REACT_APP_SENTRY_DSN=https:/batata@o95682.ingest.sentry.io/5266721
```
