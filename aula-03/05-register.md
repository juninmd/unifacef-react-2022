
## Register - Formulários

Vamos criar a requisição dos correios

```text
./src/apis/correios.api.ts
```

Coloque o conteúdo

```ts
import axios from 'axios';
import { configs } from '../configs';

const baseURL = configs.apis.correios;

export interface GetZipCode {
  resultado: string;
  resultado_txt: string;
  uf: string;
  cidade: string;
  bairro: string;
  tipo_logradouro: string;
  logradouro: string;
}

export const getZipCode = async (zipCode: number) => {
  const request = await axios.request<GetZipCode>({ baseURL, params: { cep: zipCode, formato: 'jsonp' } })
  if (request.data.cidade === '') {
    throw new Error('Cep não encontrado')
  }
  return request;
}
```

> src/components/cep/index.tsx

```tsx
import React, { useState, useEffect } from 'react';
import { getZipCode, GetZipCode } from '../../apis/correios.api';
import { Card, Segment, Loader, Image, Icon } from 'semantic-ui-react';
import Swal from 'sweetalert2';

interface Props {
  zipCode?: number;
}

export default function Cep(props: Props) {

  const [address, setAddress] = useState<GetZipCode>();

  useEffect(() => {

    const addressBlank: GetZipCode = {
      bairro: '',
      cidade: '',
      logradouro: '',
      resultado: '',
      resultado_txt: '',
      tipo_logradouro: '',
      uf: ''
    }

    async function getNewAddress(zipCode: number) {
      try {
        const response = await getZipCode(zipCode);
        setAddress(response.data);
      } catch (error) {
        Swal.fire(error.message, '', 'warning')
        setAddress(addressBlank);
      }
    }

    if (props.zipCode?.toString().length === 8) {
      getNewAddress(props.zipCode);
    } else {
      setAddress(undefined);
    }
  }, [props])

  if (!address && !props.zipCode) {
    return <Segment><p>Informe um número de CEP</p></Segment>
  }

  if (!address?.cidade) {
    return <Segment>
      <Loader active={true} />
      <Image src='https://react.semantic-ui.com/images/wireframe/short-paragraph.png' />
    </Segment>
  }

  return (
    <Card>
      <Card.Content>
        <Card.Header>{address.cidade} - {address.uf}</Card.Header>
        <Card.Meta>{address.bairro}</Card.Meta>
        <Card.Description>
          {address.tipo_logradouro} {address.logradouro}
        </Card.Description>
      </Card.Content>
      <Card.Content extra={true}>
        <Icon name='tree' />{props.zipCode}
      </Card.Content>
    </Card>
  )

}
```

Criar arquivo em:

```text
src/utils/object.util.ts
```

```ts
export function assign(obj: any, prop: string | string[], value: any) {

  if (prop === null || prop === undefined) {
    throw new Error('Propertie name undefined');
  }

  if (typeof prop === 'string') {
    prop = prop.split('.');
  }

  if (prop.length > 1) {
    const e = prop.shift();
    assign(obj[e!] =
      Object.prototype.toString.call(obj[e!]) === '[object Object]'
        ? obj[e!]
        : {},
      prop,
      value);
  } else {
    obj[prop[0]] = value;
  }
}
```

> src/containers/register/store.ts

```tsx
import { observable, action, makeAutoObservable } from 'mobx';
import { assign } from '../../utils/object.util';

export default class RegisterStore {
  constructor() {
    makeAutoObservable(this)
  }

  @observable zipcode?: number;
  @observable github?: string;

  @action handleForm = (event: any, select?: any) => {
    const { name, value } = select || event.target;
    assign(this, name, value);
  }
}

const register = new RegisterStore();
export { register };
```

> src/containers/register/index.tsx

```tsx
import * as React from 'react';
import { Container, Grid, Header, Form } from 'semantic-ui-react';
import { inject, observer } from 'mobx-react';
import NewRouterStore from '../../mobx/router.store';
import RegisterStore from './store';
import Cep from '../../components/cep';

interface Props {
  router: NewRouterStore;
  register: RegisterStore;
}

@inject('router', 'register')
@observer
export default class Register extends React.Component<Props> {

  render() {
    const { zipcode, handleForm } = this.props.register;

    return (
      <Container>
        <Grid divided='vertically'>
          <Grid.Row columns={2}>
            <Grid.Column>
              <Header color='blue' as='h2'>
                <Header.Content>
                  Register
                  <Header.Subheader>Simulação de formulário</Header.Subheader>
                </Header.Content>
              </Header>
            </Grid.Column>
          </Grid.Row>
        </Grid>
        <Form>
          <Form.Group widths='equal'>
            <Form.Field>
              <label>Informe o CEP:</label>
              <input value={zipcode || ''} maxLength={8} name='zipcode' onChange={handleForm} placeholder='Ex 14405123' />
            </Form.Field>
            <Form.Field>
              <Cep zipCode={zipcode} />
            </Form.Field>
          </Form.Group>
        </Form>
      </Container>
    )
  }
}
```

> src/apis/github.api.ts

```ts
import { configs } from '../configs';
import axios from 'axios';

const baseURL = configs.apis.github;

export interface GetGithub {
  login: string;
  id: number;
  node_id: string;
  avatar_url: string;
  gravatar_id: string;
  url: string;
  html_url: string;
  followers_url: string;
  following_url: string;
  gists_url: string;
  starred_url: string;
  subscriptions_url: string;
  organizations_url: string;
  repos_url: string;
  events_url: string;
  received_events_url: string;
  type: string;
  site_admin: boolean;
  name: string;
  company?: any;
  blog: string;
  location: string;
  email?: any;
  hireable?: any;
  bio?: any;
  twitter_username?: any;
  public_repos: number;
  public_gists: number;
  followers: number;
  following: number;
  created_at: Date;
  updated_at: Date;
}

export const getUser = (userName: string) => {
  return axios.request<GetGithub>({ baseURL, url: `users/${userName}` })
}
```

---
> src/components/github/index.tsx

```tsx
import React, { useState, useEffect } from 'react';
import { getUser, GetGithub } from '../../apis/github.api';
import { Card, Segment, Loader, Image, Icon } from 'semantic-ui-react';

interface Props {
  userName?: string;
}

export default function Github(props: Props) {

  const [profile, setProfile] = useState<GetGithub>();

  useEffect(() => {

    async function getProfile(userName: string) {
      try {
        const response = await getUser(userName);
        setProfile(response.data);
      } catch (error) {
        setProfile(undefined);
      }
    }

    if (props.userName) {
      getProfile(props.userName)
    } else {
      setProfile(undefined);
    }
  }, [props])

  if (!props.userName) {
    return <Segment>
      <p>Informe um perfil</p>
    </Segment>
  }

  if (!profile) {
    return <Segment>
      <Loader active={true} />
      <Image src='https://react.semantic-ui.com/images/wireframe/short-paragraph.png' />
    </Segment>
  }

  const openGithub = (url: string) => {
    window.open(url, 'blank');
  }

  return (
    <Card onClick={() => openGithub(profile.html_url)}>
      <Image size='small' src={profile.avatar_url} wrapped ui={false} />
      <Card.Content>
        <Card.Header>{profile.name}</Card.Header>
        <Card.Meta>{profile.login}</Card.Meta>
        <Card.Description>
          <p>{profile.bio}</p>
          <p>{profile.blog}</p>
          <p>{profile.company}</p>
          <p>{profile.email}</p>
          <p>@{profile.twitter_username}</p>
        </Card.Description>
      </Card.Content>
      <Card.Content extra={true}>
        <Icon name='tree' /> {profile.location}
      </Card.Content>
    </Card>
  )
}
```

> src/containers/register/index.tsx

```tsx
import * as React from 'react';
import { Container, Grid, Header, Form } from 'semantic-ui-react';
import { inject, observer } from 'mobx-react';
import NewRouterStore from '../../mobx/router.store';
import RegisterStore from './store';
import Cep from '../../components/cep';
import Github from '../../components/github';

interface Props {
  router: NewRouterStore;
  register: RegisterStore;
}

@inject('router', 'register')
@observer
export default class Register extends React.Component<Props> {

  render() {
    const { zipcode, handleForm, github } = this.props.register;

    return (
      <Container>
        <Grid divided='vertically'>
          <Grid.Row columns={2}>
            <Grid.Column>
              <Header color='blue' as='h2'>
                <Header.Content>
                  Register
                  <Header.Subheader>Simulação de formulário</Header.Subheader>
                </Header.Content>
              </Header>
            </Grid.Column>
          </Grid.Row>
        </Grid>
        <Form>
          <Form.Group widths='equal' style={{ alignItems: 'flex-end' }} >
            <Form.Field>
              <label>Informe o CEP:</label>
              <input value={zipcode || ''} maxLength={8} name='zipcode' onChange={handleForm} placeholder='Ex 14405123' />
            </Form.Field>
            <Form.Field>
              <Cep zipCode={zipcode} />
            </Form.Field>
          </Form.Group>
          <Form.Group widths='equal' style={{ alignItems: 'flex-end' }} >
            <Form.Field>
              <label>Informe o seu github:</label>
              <input value={github || ''} maxLength={100} name='github' onChange={handleForm} placeholder='Ex juninmd' />
            </Form.Field>
            <Form.Field>
              <Github userName={github} />
            </Form.Field>
          </Form.Group>
        </Form>
      </Container>
    )
  }
}
```
