
## Cache / Tags

Crie uma pasta chamada cache dentro de containers

Adicione o seguinte conteúdo criando o arquivo store.ts dentro

```text
src/containers/cache/store.ts
```

```ts
import { observable, action, makeAutoObservable } from 'mobx';
import { assign } from '../../utils/object.util';

export default class CacheStore {
  constructor() {
    makeAutoObservable(this)
  }

  @observable session: string | null = '';

  @observable local: string | null = '';

  @observable cookie: string | null = '';

  @observable indexed: string | null = '';

  @action handleForm = (event: any, select?: any) => {
    const { name, value } = select || event.target;
    assign(this, name, value);
  }

  @action saveCookie = () => {
    document.cookie = `unifacef=${this.cookie}`
  }

  @action saveLocalStorage = () => {
    localStorage.setItem('unifacef-local', this.local || '');
  }

  @action saveSessionStorage = () => {
    sessionStorage.setItem('unifacef-session', this.session || '');
  }

  @action loadForm = () => {
    this.cookie = this.getCookie('unifacef')
    this.session = sessionStorage.getItem('unifacef-session');
    this.local = localStorage.getItem('unifacef-local');
  }

  @action submit = () => {
    this.saveCookie();
    this.saveSessionStorage();
    this.saveLocalStorage();
  }

  getCookie(cname) {
    const name = `${cname}=`
    const decodedCookie = decodeURIComponent(document.cookie);
    const ca = decodedCookie.split(';');
    for (let i = 0; i < ca.length; i++) {
      const c = ca[i];
      while (c.charAt(0) === ' ') {
        c.substring(1);
      }
      if (c.indexOf(name) === 0) {
        return c.substring(name.length, c.length);
      }
    }
    return "";
  }
}
const cache = new CacheStore();
export { cache };

```

Adicione o seguinte conteúdo criando o arquivo index.tsx dentro

```text
src/containers/cache/index.tsx
```

```tsx
import * as React from 'react';
import { Container, Grid, Header, Form, Button } from 'semantic-ui-react';
import { inject, observer } from 'mobx-react';
import NewRouterStore from '../../mobx/router.store';
import CacheStore from './store';

interface Props {
  router: NewRouterStore;
  cache: CacheStore;
}

@inject('router', 'cache')
@observer
export default class Cache extends React.Component<Props>{

  componentDidMount() {
    const { loadForm } = this.props.cache;
    loadForm();
  }
  render() {

    const { local, session, cookie, handleForm, submit } = this.props.cache;

    const submitForm = (e) => {
      e.preventDefault();
      submit();
    }

    return (
      <Container>
        <Grid divided='vertically'>
          <Grid.Row columns={2}>
            <Header color='blue' as='h2'>
              <Header.Content>
                Cache
                <Header.Subheader>Local Storage / Session Storage / Cookies</Header.Subheader>
              </Header.Content>
            </Header>
          </Grid.Row>
        </Grid>
        <Form onSubmit={submitForm}>
          <Form.Group widths='equal'>
            <Form.Field>
              <label>Local Storage</label>
              <input value={local || ''} name='local' onChange={handleForm} placeholder='Batata' />
            </Form.Field>
            <Form.Field>
              <label>Session Storage</label>
              <input value={session || ''} name='session' onChange={handleForm} placeholder='Maça' />
            </Form.Field>
            <Form.Field>
              <label>Cookie</label>
              <input value={cookie || ''} name='cookie' onChange={handleForm} placeholder='Beterraba' />
            </Form.Field>
          </Form.Group>
          <Button type='submit'>Armazenar</Button>
        </Form>
      </Container>)
  }
}
```

Adicionar store no mobx

```text
src/mobx/index.ts
```

```ts
import { home } from '../containers/home/store';
import { combustivel } from '../containers/combustivel/store';
import { router } from './router.store';
import { starWars } from '../containers/star-wars/store';
import { starWarsDetails } from '../containers/star-wars-details/store';
import { cache } from '../containers/cache/store';

export {
  router,
  combustivel,
  home,
  starWars,
  starWarsDetails,
  cache,
}
```

Adicione a nova rota `cache`

```ts
  { path: `${publicUrl}/cache`, name: 'Cache', component: Cache, exact: true },
```