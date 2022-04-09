## Variáveis de ambiente

> src/configs/index.ts

```ts
export const configs = {
  apis: {
    cep: process.env.REACT_APP_CEP_URL,
    economia: process.env.REACT_APP_ECONOMIA_URL,
    github: process.env.REACT_APP_GITHUB_URL,
    starWars: process.env.REACT_APP_STAR_WARS_BASE_URL,
  },
  sentry: process.env.REACT_APP_SENTRY_DSN
}
```

## Envs

Coloque todas as variáveis do seu projeto dentro do arquivo .env,
é só pesquisar em tudo que criamos no projeto que comece com

```env
process.env.REACT_APP_*
```

Ficando assim por exemplo

```.env
REACT_APP_CEP_URL=http://cep.republicavirtual.com.br/web_cep.php
REACT_APP_ECONOMIA_URL=https://economia.awesomeapi.com.br/json/all
REACT_APP_GITHUB_URL=https://api.github.com
REACT_APP_STAR_WARS_BASE_URL=https://star-wars-api-unifacef.herokuapp.com
REACT_APP_SENTRY_DSN=<?>
```
