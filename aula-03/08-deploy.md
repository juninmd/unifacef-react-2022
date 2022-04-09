## Deploy

Faça o build do projeto

```bash
yarn build
```

Instale o servidor local e habilite o projeto

```bash
  yarn global add serve // npm install -g serve
  serve -s build
```


## Corrigir problema de rotas

Criar arquivo em

> public/_redirects

```text
/* /index.html 200
```

## Envs Fix

Abra o .gitignore
e apague a linha .env.local

Vamos fazer o deploy na nuvem do projeto
<https://app.netlify.com/start>