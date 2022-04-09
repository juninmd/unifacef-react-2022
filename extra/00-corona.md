## Corona Api

.env

```
REACT_APP_CORONA_URL=https://api.covid19api.com
```

> src/apis/corona.api.ts

```ts
import axios from 'axios';
import { configs } from '../configs';

const baseURL = configs.apis.corona;

export interface IGlobal {
  NewConfirmed: number;
  TotalConfirmed: number;
  NewDeaths: number;
  TotalDeaths: number;
  NewRecovered: number;
  TotalRecovered: number;
}

export interface ICountry {
  Country: string;
  CountryCode: string;
  Slug: string;
  NewConfirmed: number;
  TotalConfirmed: number;
  NewDeaths: number;
  TotalDeaths: number;
  NewRecovered: number;
  TotalRecovered: number;
  Date: Date;
}

export interface ISummary {
  Global: IGlobal;
  Countries: ICountry[];
  Date: Date;
}

export const getSummary = () => {
  return axios.request<ISummary>({ baseURL, url: 'summary' })
}

export interface ICountryB {
  Country: string;
  Slug: string;
  ISO2: string;
}

export const getCountries = () => {
  return axios.request<ICountryB[]>({ baseURL, url: 'countries' });
}
```

> src/containers/store.ts

```ts
import { action, observable } from 'mobx';
import { getCountries, getSummary, ISummary, ICountryB } from '../../apis/corona.api';
import { assign } from '../../utils/object.util';

export default class CoronaStore {

  @observable countryCode: string = '';

  @observable summary?: ISummary;

  @observable countries: ICountryB[] = [];

  @action handleForm = (event: any, select?: any) => {
    const { name, value } = select || event.target;
    assign(this, name, value);
  }

  @action getCountries = async () => {
    try {
      const { data } = await getCountries();
      this.countries = data;
    } catch (error) {
      this.countries = [];
    }
  }

  @action getSummary = async () => {
    try {
      const { data } = await getSummary();
      this.summary = data;
    } catch (error) {
      this.summary = undefined;
    }
  }
}
const corona = new CoronaStore();
export { corona };
```

!Danger! (código parcial)

> src/containers/corona/index.tsx

```tsx
import * as React from 'react';
import { Container, Grid, Header, Form, Dropdown, Card } from 'semantic-ui-react';
import { inject, observer } from 'mobx-react';
import NewRouterStore from '../../mobx/router.store';
import CoronaStore from './store';
import Summary from '../../components/summary';

interface Props {
  router: NewRouterStore;
  corona: CoronaStore
}
@inject('router', 'corona')
@observer
export default class Corona extends React.Component<Props>{


  async componentDidMount() {
    const { getCountries, getSummary } = this.props.corona;
    await Promise.all([getCountries(), getSummary()]);
  }

  render() {
    const { summary, countryCode, handleForm } = this.props.corona;

    return (
      <Container>
        <Grid divided='vertically'>
          <Grid.Row columns={2}>
            <Header color='blue' as='h2'>
              <Header.Content>
                Corona
              <Header.Subheader>Sumário Mundial {(new Date()).toLocaleDateString()}</Header.Subheader>
              </Header.Content>
            </Header>
          </Grid.Row>
        </Grid>

        <Grid>
          <Grid.Row>
            <Card.Group doubling={true} itemsPerRow={1}>
              <Summary global={summary?.Global} />
            </Card.Group>
          </Grid.Row>
          <Grid.Row>
            <Form>
              <Form.Group widths='equal'>
                <Form.Field>
                  <Dropdown
                    placeholder='Selecione um país'
                    fluid
                    selection
                    search={true}
                    name='countryCode'
                    value={countryCode}
                    clearable={true}
                    onChange={handleForm}
                    options={[]} />
                </Form.Field>
              </Form.Group>
            </Form>
          </Grid.Row>
        </Grid>
      </Container>
    )
  }

}
```

> src/components/summary/index.tsx

```tsx
import React from 'react';
import { Card, Feed, Grid } from 'semantic-ui-react';
import { IGlobal } from '../../apis/corona.api';

interface Props {
  global?: IGlobal;
}

export default function Summary(props: Props) {

  const { global } = props;

  if (!global) {
    return <></>
  }

  return (
    <Card color='blue' centered={true} fluid={true}>
      <Card.Content>
        <Card.Header>Mundial</Card.Header>
        <Card.Description>
          <Grid columns={2}>
            <Grid.Column>
              <Feed>
                <Feed.Event>
                  <Feed.Content>
                    <Feed.Date content='Casos' />
                    <Feed.Summary>{global.NewConfirmed}</Feed.Summary>
                  </Feed.Content>
                </Feed.Event>
                <Feed.Event>
                  <Feed.Content>
                    <Feed.Date content='Mortes' />
                    <Feed.Summary>{global.NewDeaths}</Feed.Summary>
                  </Feed.Content>
                </Feed.Event>
                <Feed.Event>
                  <Feed.Content>
                    <Feed.Date content='Recuperações' />
                    <Feed.Summary>{global.NewRecovered}</Feed.Summary>
                  </Feed.Content>
                </Feed.Event>
              </Feed>
            </Grid.Column>
            <Grid.Column>
              <Feed>
                <Feed.Event>
                  <Feed.Content>
                    <Feed.Date content='Total Casos' />
                    <Feed.Summary>{global.TotalConfirmed}</Feed.Summary>
                  </Feed.Content>
                </Feed.Event>
                <Feed.Event>
                  <Feed.Content>
                    <Feed.Date content='Total Mortes' />
                    <Feed.Summary>{global.TotalDeaths}</Feed.Summary>
                  </Feed.Content>
                </Feed.Event>
                <Feed.Event>
                  <Feed.Content>
                    <Feed.Date content='Total Recuperações' />
                    <Feed.Summary>{global.TotalRecovered}</Feed.Summary>
                  </Feed.Content>
                </Feed.Event>
              </Feed>
            </Grid.Column>
          </Grid>
        </Card.Description>
      </Card.Content>
    </Card>
  )
}
```

> src/components/country/index.tsx

```tsx
import React from 'react';
import { Card, Grid, Feed } from 'semantic-ui-react';
import { ICountry } from '../../apis/corona.api';

interface Props {
  country: ICountry;
}
export default function Country(props: Props) {

  const { country } = props;

  return (
    <Card color='red' centered={true} fluid={true}>
      <Card.Content>
        <Card.Header>{country.Country}</Card.Header>
        <Card.Description>
          <Grid columns={2}>
            <Grid.Column>
              <Feed>
                <Feed.Event>
                  <Feed.Content>
                    <Feed.Date content='Casos' />
                    <Feed.Summary>{country.NewConfirmed}</Feed.Summary>
                  </Feed.Content>
                </Feed.Event>
                <Feed.Event>
                  <Feed.Content>
                    <Feed.Date content='Mortes' />
                    <Feed.Summary>{country.NewDeaths}</Feed.Summary>
                  </Feed.Content>
                </Feed.Event>
                <Feed.Event>
                  <Feed.Content>
                    <Feed.Date content='Recuperações' />
                    <Feed.Summary>{country.NewRecovered}</Feed.Summary>
                  </Feed.Content>
                </Feed.Event>
              </Feed>
            </Grid.Column>
            <Grid.Column>
              <Feed>
                <Feed.Event>
                  <Feed.Content>
                    <Feed.Date content='Total Casos' />
                    <Feed.Summary>{country.TotalConfirmed}</Feed.Summary>
                  </Feed.Content>
                </Feed.Event>
                <Feed.Event>
                  <Feed.Content>
                    <Feed.Date content='Total Mortes' />
                    <Feed.Summary>{country.TotalDeaths}</Feed.Summary>
                  </Feed.Content>
                </Feed.Event>
                <Feed.Event>
                  <Feed.Content>
                    <Feed.Date content='Total Recuperações' />
                    <Feed.Summary>{country.TotalRecovered}</Feed.Summary>
                  </Feed.Content>
                </Feed.Event>
              </Feed>
            </Grid.Column>
          </Grid>
        </Card.Description>
      </Card.Content>
    </Card>
  )
}
```