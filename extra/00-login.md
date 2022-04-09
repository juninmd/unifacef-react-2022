
> src/containers/login/style.css

```css
.login {
  background-color: #0083ca;
-webkit-justify-content: center;
justify-content: center;
-webkit-align-items: center;
align-items: center;
height: 100%;
padding: 40px;
overflow: auto;
height: 100vh;
}
```

> src/utils/auth.util.ts

```tsx
import Swal from 'sweetalert2';

export const isLoggedIn = () => {
  const user = sessionStorage.getItem('auth_token');

  if (user === null) {
    return false;
  }

  try {
    const data = JSON.parse(user) as UserData;
    if (data.email && data.name) {
      return true;
    }
    return false;
  } catch (error) {
    return false;
  }
};

interface UserData {
  name: string;
  email: string;
}

export const getFirstName = (): string => {
  if (!isLoggedIn()) {
    return '';
  }

  return getUser().name;
};

export const getUser = (): UserData => {
  if (!isLoggedIn()) {
    logOff();
    Swal.fire('Sua sessão expirou', 'Por favor, efetue login novamente', 'error');
    window.location.href = '/login';
    throw new Error('Sua sessão expirou');
  }

  const user = sessionStorage.getItem('auth_token')!;
  return JSON.parse(user) as UserData;
};

export const setAuth = (token: string) => {
  sessionStorage.setItem('auth_token', token);
};

export const getAuth = () => {
  return sessionStorage.getItem('auth_token');
};

export const logOff = () => {
  sessionStorage.removeItem('auth_token');
};
```

> src/containers/login/store.ts

```tsx
import Swal from 'sweetalert2';
import { observable, action } from 'mobx';
import { assign } from '../../utils/object.util';
import { setAuth } from '../../utils/auth.util';

export default class LoginStore {
  @observable email: string = '';
  @observable password: string = '';

  @action handleForm = (event: any, select?: any) => {
    const { name, value } = select || event.target;
    assign(this, name, value);
  };

  @action handleSubmit = () => {

    const credentials = {
      email: 'jr_acn@yahoo.com.br',
      password: 'batata'
    };

    if (this.email !== credentials.email || this.password !== credentials.password) {
      Swal.fire('Usuário/Senha Incorreto', '', 'error');
      return false;
    }

    const data = {
      email: credentials.email,
      name: 'Antonio Carlos'
    };

    setAuth(JSON.stringify(data));

    return true;
  };

}
const login = new LoginStore();
export { login };

```

> src/containers/login/index.tsx

```tsx
import * as React from 'react';
import { Form, Input, Divider, Segment, Grid, Button, Card } from 'semantic-ui-react';
import { inject, observer } from 'mobx-react';
import NewRouterStore from '../../mobx/router.store';
import LoginStore from './store';
import { logOff, isLoggedIn } from '../../utils/auth.util';
import './style.css';
import Logo from '../../components/logo';

interface Props {
  router: NewRouterStore;
  login: LoginStore;
  match: any;
}

@inject('router', 'login')
@observer
export default class Login extends React.Component<Props> {

  componentWillMount = () => {
    const { path } = this.props.match;
    if (path === '/logout') {
      logOff();
      return;
    }

    const { setHistory } = this.props.router;

    if (isLoggedIn()) {
      setHistory('home');
    }
  }

  handleSubmit = async (event: any) => {
    event.preventDefault();
    const { handleSubmit } = this.props.login;
    const logged = handleSubmit();
    if (logged) {
      const { setHistory } = this.props.router;
      setHistory('home');
    }
  }

  render() {
    const { handleForm, password, email } = this.props.login;
    return (
      <section className='login'>
        <Divider hidden={true} />

        <Form size='large' onSubmit={this.handleSubmit}>
          <Card.Group centered>
            <Card centered>
              <Card.Content>
                <Logo />
              </Card.Content>
            </Card>
          </Card.Group>
          <Grid textAlign='center' style={{ height: '100%' }} verticalAlign='middle'>
            <Grid.Column width='4'>
              <Segment color='green'>

                <Form.Field>
                  <label>E-Mail:</label>
                  <Input
                    name='email'
                    minLength={3}
                    maxLength={20}
                    className={'uppercase'}
                    placeholder='ex: jr_acn@yahoo.com.br'
                    value={email}
                    type='email'
                    icon='user'
                    iconPosition={'left'}
                    onChange={handleForm}
                    required={true} />
                </Form.Field>

                <Form.Field>
                  <label>Senha</label>
                  <Input
                    type='password'
                    name='password'
                    value={password}
                    placeholder='EX: 123'
                    onChange={handleForm}
                    icon='lock'
                    iconPosition={'left'}
                    minLength={3}
                    maxLength={15}
                    autoComplete={'current-password'}
                    required={true} />
                </Form.Field>

                <Form.Field width='6'>
                  <Button icon={'unlock'} labelPosition={'left'} fluid={true} positive={true} content={'Acessar'} title='Acessar' />
                </Form.Field>

              </Segment>
            </Grid.Column>
          </Grid>

        </Form >

      </section>
    );
  }
}
```

> src/routes/endpoints.ts

```ts
import Home from "../containers/home";
import { RouteProps } from "react-router-dom";
import Sobre from "../containers/sobre";
import Combustivel from "../containers/combustivel";
import StarWars from "../containers/star-wars";
import StarWarsDetails from "../containers/star-wars-details";
import Cache from "../containers/cache";
import Tags from "../containers/tags";
import Register from "../containers/register";
import Corona from "../containers/corona";
import Login from "../containers/login";

const publicUrl = process.env.PUBLIC_URL;

interface EndPointsProps extends RouteProps {
  name?: string
}

export const endpoints: EndPointsProps[] = [
  { path: `${publicUrl}/home`, name: 'Home', component: Home, exact: true },
  { path: `${publicUrl}/combustivel`, name: 'Combustível', component: Combustivel, exact: true },
  { path: `${publicUrl}/star-wars`, name: 'Star Wars', component: StarWars, exact: true },
  { path: `${publicUrl}/star-wars/:id`, component: StarWarsDetails, exact: true },
  { path: `${publicUrl}/cache`, name: 'Cache', component: Cache, exact: true },
  { path: `${publicUrl}/tags`, name: 'Tags', component: Tags, exact: true },
  { path: `${publicUrl}/register`, name: 'Register', component: Register, exact: true },
  { path: `${publicUrl}/corona`, name: 'Corona', component: Corona, exact: true },
  { path: `${publicUrl}/sobre`, name: 'Sobre', component: Sobre, exact: true },
];

export const loginEndpoints: EndPointsProps[] = [
  { path: `${publicUrl}/`, component: Login, exact: true },
  { path: `${publicUrl}/logout`, component: Login, exact: true },
  { path: `${publicUrl}/login`, component: Login, exact: true },
];
```

> src/routes/index.tsx

```tsx
import * as React from "react"

import { Route, Switch, withRouter, Redirect } from "react-router-dom";

import { Divider } from "semantic-ui-react";
import MainMenu from "../components/main-menu";
import NotFound from "../containers/not-found";
import { endpoints, loginEndpoints } from "./endpoints";
import { observer } from "mobx-react";
import { isLoggedIn } from "../utils/auth.util";

// @ts-ignore
@withRouter
@observer
export default class Routes extends React.Component {

  render() {
    return (
      <>
        {loginEndpoints.map((route, key) => (
          <Route key={key} {...route} />
        ))}
        {isLoggedIn() ?
          <>
            <MainMenu />
            <Divider hidden={true} />
            <Switch>
              {endpoints.map((route, key) => (
                <Route key={key} {...route} />
              ))}
              <Route path='*' exact={true} render={props => <NotFound {...props} />} />
            </Switch>
          </> : <Redirect to={{ pathname: 'login' }} />
        }
      </>
    )
  }
}
```