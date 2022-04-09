## Sobre

Crie uma nova pasta dentro de containers chamada sobre

Adicione um arquivo index.tsx

```text
src/containers/sobre/index.tsx
```

```tsx
import * as React from 'react';
import { Container, Grid, Header } from 'semantic-ui-react';
import { inject, observer } from 'mobx-react';
import NewRouterStore from '../../mobx/router.store';

interface Props {
  router: NewRouterStore;
}

@inject('router')
@observer
export default class Sobre extends React.Component<Props> {
  render() {
    return (
      <Container>
        <Grid divided='vertically'>
          <Grid.Row columns={2}>
            <Grid.Column>
              <Header color='blue' as='h2'>
                <Header.Content>
                  Sobre
                 <Header.Subheader>Um pouco sobre mim</Header.Subheader>
                </Header.Content>
              </Header>
            </Grid.Column>
          </Grid.Row>
        </Grid>
      </Container>
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
import Sobre from "../containers/Sobre";
```

Rota
```tsx
  { path: `${publicUrl}/sobre`, name: 'Sobre', component: Sobre, exact: true },
```