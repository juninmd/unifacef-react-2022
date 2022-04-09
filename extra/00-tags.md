## Tags

Crie uma pasta chamada tags dentro de containers

Adicione o seguinte conteúdo criando o arquivo store.ts dentro

```text
src/containers/tags/store.ts
```

```tsx
import swal from 'sweetalert2';
import { action, observable } from 'mobx';

export default class TagsStore {

  @observable image: string | null = 'https://www.einerd.com.br/wp-content/uploads/2019/08/Naruto-erro-1%C2%BA-epis%C3%B3dio-capa-890x466.jpg';
  @observable video: string | null = 'https://www.w3schools.com/html/mov_bbb.mp4';
  @observable geoLocale = '';

  @action getLocationGPS = () => {
    if (navigator.geolocation) {
      navigator.geolocation.getCurrentPosition((pos) => {
        this.geoLocale = `Latitude: ${pos.coords.latitude} - Longitude: ${pos.coords.longitude}`;
      }, (err) => {
        swal.fire(err.message, '', 'error');
      }, {
        enableHighAccuracy: true,
        timeout: 5000
      });
    } else {
      swal.fire('Geolocation não é permitido nesse navegador', '', 'error')
    }
  }

  getUserMedia(constraints) {
    if (navigator.mediaDevices) {
      return navigator.mediaDevices.getUserMedia(constraints);
    }

    const legacyApi = navigator.getUserMedia;
    if (legacyApi) {
      return new Promise(function (resolve, reject) {
        legacyApi.bind(navigator)(constraints, resolve, reject);
      })
    }
    throw new Error('not suported');
  }

  @action getStream = async (type) => {
    const constraints = {};
    constraints[type] = true;

    try {
      const stream = await this.getUserMedia(constraints);
      const mediaControl = document.querySelector('video#webcam') as any;
      if ('srcObject' in mediaControl) {
        mediaControl.srcObject = stream;
      } else {
        mediaControl.src = (window.URL || window.webkitURL).createObjectURL(stream);
      }
      mediaControl.play();
    } catch (error) {
      swal.fire(error.message, '', 'error');
    }
  }
}

const tags = new TagsStore();
export { tags };
```

Adicione o seguinte conteúdo criando o arquivo index.tsx dentro

```text
src/containers/tags/index.tsx
```

```tsx
import * as React from 'react';
import { Container, Grid, Header, Form } from 'semantic-ui-react';
import { inject, observer } from 'mobx-react';
import NewRouterStore from '../../mobx/router.store';
import tagsStore from './store';

interface Props {
  router: NewRouterStore;
  tags: tagsStore;
}

@inject('router', 'tags')
@observer
export default class Tags extends React.Component<Props> {

  render() {

    const { image, video, getLocationGPS, geoLocale, getStream } = this.props.tags;

    return (
      <Container>
        <Grid divided='vertically'>
          <Grid.Row columns={2}>
            <Grid.Column>
              <Header color='blue' as='h2'>
                <Header.Content>
                  Tags
                 <Header.Subheader>Um pouco de HTML5 &#128512;</Header.Subheader>
                </Header.Content>
              </Header>
            </Grid.Column>
          </Grid.Row>
        </Grid>
        <Form>
          <Form.Group widths='equal'>
            <Form.Field>
              <label>Video</label>
              {video && <video src={video} muted={true} controls={true} autoPlay={true} />}
            </Form.Field>
            <Form.Field>
              <label>Imagem</label>
              {image && <img height={200} alt='imagem' src={image} />}
            </Form.Field>
          </Form.Group>
          <Form.Group widths='equal'>
            <Form.Field>
              <label>Youtube</label>
              <iframe title='Youtube' width="420" height="315"
                src="https://www.youtube.com/embed/tgbNymZ7vqY">
              </iframe>
            </Form.Field>
            <Form.Field>
              <button onClick={() => getLocationGPS()}>Pegar Coordenadas</button>
              <p>{geoLocale}</p>
            </Form.Field>
          </Form.Group>
          <Form.Group widths='equal'>
            <Form.Field>
              <p><button type="button" onClick={() => getStream('video')}>Ativar Web Cam</button></p>
              <video id='webcam' controls autoPlay={true} style={{ height: '180px', width: '240px' }}></video>
            </Form.Field>
          </Form.Group>
        </Form>
      </Container >
    );
  }
}
```

Adicione a nova rota `tags`

```ts
  { path: `${publicUrl}/tags`, name: 'Tags', component: Tags, exact: true },
```

Adicionar store no mobx

```text
src/mobx/index.ts
```