## Not Found

Crie uma nova pasta chamada not-found

```text
src/containers/not-found/index.tsx
```

```tsx
import * as React from 'react';
import { Container } from 'semantic-ui-react';

export default class NotFound extends React.Component {

  render() {
    return (
      <Container><p>Página não encontrada!</p></Container>
    );
  }
}
```

```text
./src/routes/index.tsx
```

```tsx
import * as React from 'react';
import { observer } from 'mobx-react';
import {
  Route,
  withRouter,
  Switch,
} from 'react-router-dom';
import { Divider } from 'semantic-ui-react';
import NotFound from '../containers/not-found';
import { endpoints } from './endpoints';

// @ts-ignore
@withRouter
@observer
export default class Routes extends React.Component {

  render() {
    return (
      <>
        <Divider hidden={true} />
        <Switch>
          {endpoints.map((route, i) => (
            <Route key={i} {...route} />)
          )}
          <Route path='*' exact={true} render={props => <NotFound {...props} />} />
        </Switch>
      </>
    );
  }
}
```


Adicionar novo endpoint em

```text
src/routes/endpoints.ts
```

Import
```ts
import NotFound from "../containers/NotFound";
```

Rota
```tsx
  { path: `${publicUrl}/404`, name: 'NotFound', component: NotFound, exact: true },
```