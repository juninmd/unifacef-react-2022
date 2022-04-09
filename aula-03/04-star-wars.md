
## Star Wars

## Env

```
REACT_APP_STAR_WARS_URL=https://star-wars-api-unifacef.herokuapp.com
```

Crie uma nova pasta chamada star-wars dentro de apis

Crie um novo arquivo

```text
./src/apis/star-wars.api.ts
```

```tsx
import axios from 'axios';
import { configs } from '../configs';

const baseURL = configs.apis.starWars;

export const getFilms = () => {
  return axios.request({ baseURL, url: 'films' })
}

export const getFilmById = (id: number) => {
  return axios.request({ baseURL, url: `films/${id}` })
}
```

Crie uma nova pasta chamada star-wars dentro de containers

Crie um novo arquivo

```text
src/containers/star-wars/store.ts
```

```tsx
import { action, observable } from 'mobx';
import { getFilms } from '../../apis/star-wars.api';

export default class StarWarsStore {
  @observable films: any[] = [];

  @action buildFilms = async () => {
    const { data } = await getFilms();
    this.films = data;
  }

}
const starWars = new StarWarsStore();
export { starWars };
```

Adicione a store na lista de stores

```text
src/mobx/index.ts
```

```tsx
import { router } from './router.store';
import { home } from '../containers/home/store';
import { combustivel } from '../containers/combustivel/store';
import { starWars } from '../containers/star-wars/store';

export {
  router,
  home,
  combustivel,
  starWars
};
```

Crie um novo arquivo

```text
src/containers/star-wars/index.tsx
```

```tsx
import * as React from 'react';
import { Container, Card, Grid, Header, Image } from 'semantic-ui-react';
import { inject, observer } from 'mobx-react';
import NewRouterStore from '../../mobx/router.store';
import StarWarsStore from './store';

interface Props {
  router: NewRouterStore;
  starWars: StarWarsStore;
}

@inject('router', 'starWars')
@observer
export default class StarWars extends React.Component<Props> {

  async componentDidMount() {
    const { buildFilms } = this.props.starWars;
    await buildFilms();
  }

  render() {

   const { films } = this.props.starWars;

    return (
      <Container>
        <Grid divided='vertically'>
          <Grid.Row columns={2}>
            <Grid.Column>
              <Header color='blue' as='h2'>
                <Header.Content>
                  Star Wars
                 <Header.Subheader>Lista de filmes</Header.Subheader>
                </Header.Content>
              </Header>
            </Grid.Column>
          </Grid.Row>
        </Grid>
        <Card.Group itemsPerRow={2}>
          {films.map((film, index) => {
            return (
              <Card key={index}>
                <Image src={film.photo} wrapped ui={false} size='small' />
                <Card.Content>
                  <Card.Meta>{film.title}</Card.Meta>
                  <Card.Description>Episode {film.episode_id.toString()}</Card.Description>
                </Card.Content>
              </Card>)
          })}
        </Card.Group>
      </Container>
    );
  }
}

```

Crie uma nova pasta chamada
`star-wars-details`
dentro de containers

Crie um novo arquivo

```text
src/containers/star-wars-details/store.ts
```

```ts
import { action, observable } from 'mobx';
import { getFilmById } from '../../apis/star-wars.api';

export default class StarWarsDetailsStore {
  @observable film: any = {};

  @action buildFilmById = async (id: number) => {
    const { data } = await getFilmById(id);
    this.film = data;
  }

}
const starWarsDetails = new StarWarsDetailsStore();
export { starWarsDetails };

```

Crie um novo arquivo

```text
src/containers/star-wars-details/index.tsx
```

```tsx
import * as React from 'react';
import { Container, Card, Grid, Header, Image, List } from 'semantic-ui-react';
import { inject, observer } from 'mobx-react';
import NewRouterStore from '../../mobx/router.store';
import StarWarsDetailsStore from './store';
import { RouteComponentProps } from 'react-router-dom';

interface Props {
  router: NewRouterStore;
  starWarsDetails: StarWarsDetailsStore;
}

@inject('router', 'starWarsDetails')
@observer
export default class StarWarsDetails extends React.Component<RouteComponentProps<{ id: string }> & Props>{

  async componentDidMount() {
    const { buildFilmById } = this.props.starWarsDetails;
    const id = Number(this.props.match.params.id);
    await buildFilmById(id);
  }

  render() {

    const { film } = this.props.starWarsDetails;

    return (
      <Container>
        <Grid divided='vertically'>
          <Grid.Row columns={2}>
            <Header color='blue' as='h2'>
              <Header.Content>
                Star Wars
                <Header.Subheader>
                  Detalhe do filme
                </Header.Subheader>
              </Header.Content>
            </Header>
          </Grid.Row>
        </Grid>
        <Card.Group>
          <Card>
            <Image src={film.photo} wrapped ui={false} size='small' />
            <Card.Content>
              <Card.Meta>{film.title} - Episode {film.episode_id}</Card.Meta>
            </Card.Content>
            <Card.Content extra>
              <p>Director: {film.director}</p>
              <p>Release Date: {film.release_date}</p>
            </Card.Content>
            <Card.Content>
              <p>Personagens:</p>
              <List divided={true} relaxed={true}>
                {film.characters?.map((character, indexChar) => {
                  return (
                    <List.Item key={indexChar}>
                      {character.name}
                    </List.Item>
                  )
                })}
              </List>
            </Card.Content>
          </Card>
        </Card.Group>
      </Container>
    )
  }
}
```

Adicionar novo endpoint em

```text
src/routes/endpoints.ts
```

```ts
import StarWarsDetails from "../containers/star-wars-details";
```

```ts
  { path: `${publicUrl}/star-wars/:id`, component: StarWarsDetails, exact: true },
```

Atualize a store

```text
src/mobx/index.ts
```

```ts
import { home } from '../containers/home/store';
import { combustivel } from '../containers/combustivel/store';
import { router } from './router.store';
import { starWars } from '../containers/star-wars/store';
import { starWarsDetails } from '../containers/star-wars-details/store';

export {
  router,
  combustivel,
  home,
  starWars,
  starWarsDetails,
}
```