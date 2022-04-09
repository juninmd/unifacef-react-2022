# Eleições

## Store

Em
```
./src/containers/eleicoes/store.ts
```

```ts
import { action, computed, makeAutoObservable, observable } from 'mobx';

import Swal from 'sweetalert2';

export default class EleicoesStore {
  constructor() {
    makeAutoObservable(this)
  }

  @observable candidateNumber = '';

  @observable candidates = {
    ['1']: {
      name: 'Naruto',
      party: 'Konoha',
      image: 'https://cm-santiago-do-cacem.pt/img/fantasy/83/how-watch-naruto-order.jpg',
      votes: 0
    },
    ['2']: {
      name: 'Goku',
      party: 'Capsule Corp',
      image: 'https://tm.ibxk.com.br/2020/12/16/16032833123011.jpg',
      votes: 0
    },
    ['3']: {
      name: 'Batman',
      party: 'Wayne Corp',
      image: 'https://veja.abril.com.br/wp-content/uploads/2022/03/batman-oscar.jpg?quality=70&strip=info&w=680&h=453&crop=1',
      votes: 0
    },
    ['branco']: {
      name: 'Branco',
      party: 'Nenhum',
      image: 'https://cdn.mos.cms.futurecdn.net/uRfKogf9KpxZDLUJR4EnjK.jpeg',
      votes: 0
    },
    ['00000']: {
      name: 'Nulo',
      party: 'Nenhum',
      image: 'https://www.teclasap.com.br/wp-content/uploads/2014/03/voto-nulo.png',
      votes: 0
    }
  };

  @computed get getButtonsNumbers() {
    const keys = Array.from({ length: 9 }, (_, i) => i + 1);
    keys.push(0);
    return keys;
  }

  @computed get getCandidate() {
    return this.candidates[this.candidateNumber];
  }

  @computed get getCandidatesVotes() {
    return Object.values(this.candidates).sort((a, b) => (a.votes > b.votes ? -1 : 1))
  }

  @action handleCandidateNumber = (candidateNumber: string) => {
    if (this.candidateNumber === 'branco') {
      this.candidateNumber = '';
    }
    if (this.candidateNumber.length === 5) {
      Swal.fire("Número máximo é de 5 caracteres", '', 'warning');
      return;
    }
    this.playSound(500);
    this.candidateNumber += candidateNumber;
  };

  @action handleFix = () => {
    if (this.candidateNumber === 'branco') {
      this.candidateNumber = '';
    }
    if (this.candidateNumber.length > 0) {
      this.playSound(500);
      this.candidateNumber = this.candidateNumber.substring(0, this.candidateNumber.length - 1);
    }
  };

  @action vote = () => {
    if (this.candidates[this.candidateNumber]) {
      this.candidates[this.candidateNumber].votes = this.candidates[this.candidateNumber].votes + 1;
      Swal.fire("Voto computado com sucesso", "O Brasil agradece!", 'success');
      this.candidateNumber = '';
      this.playSound(2000);
    } else {
      Swal.fire("Número Inválido de candidato", '', 'warning');
    }
  };

  @action voteWhite = () => {
    this.playSound(500);
    this.candidateNumber = 'branco';
  };

  @action playSound = (timeout: number) => {
    const audio = new Audio('https://www.adautobulhoes.com.br/locutor/baixar-som-de-urna-eletronica-download-urna-eletronica-som-urna-eletronica-mp3-barulho-urna-eletronica-mp3-toque-urna-eletronica-mp3-efeito-urna-eletronica-mp3-barulho-urna-eletronica-mp3-download-jingle-politico-vereador-e-.mp3');
    audio.play();
    setTimeout(() => {
      audio.pause();
    }, timeout);
  };

}
const eleicoes = new EleicoesStore();
export { eleicoes };

```

## HTML

Em
```
./src/containers/eleicoes/index.tsx
```

Crie

```tsx
import * as React from 'react';
import { Container, Grid, Header, Card, Image, Input, List, Segment, ButtonGroup } from 'semantic-ui-react';
import { inject, observer } from 'mobx-react';
import NewRouterStore from '../../mobx/router.store';
import EleicoesStore from './store';
import './style.css';

interface Props {
  router: NewRouterStore;
  eleicoes: EleicoesStore;
}

@inject('router', 'eleicoes')
@observer
export default class Eleicoes extends React.Component<Props> {

  render() {

    const {
      candidateNumber,
      getButtonsNumbers,
      getCandidate,
      handleCandidateNumber,
      handleFix,
      vote,
      voteWhite,
      getCandidatesVotes,
    } = this.props.eleicoes;

    return (
      <Container>
        <Grid divided='vertically'>
          <Grid.Row columns={2}>
            <Grid.Column>
              <Header color='blue' as='h2'>
                <Header.Content>
                  Eleições 2022
                  <Header.Subheader>Decida o destino do Brasil!</Header.Subheader>
                </Header.Content>
              </Header>
            </Grid.Column>
          </Grid.Row>
        </Grid>
        <Grid>
          <Grid.Column width={7}>
            <Card.Group>
              <Card fluid={true}>
                <Card.Content>
                  <Input fluid={true} type='text' placeholder='Digite no painel o número do seu candidato.'> <input readOnly value={candidateNumber} />{ }</Input>
                </Card.Content>
              </Card>
              {getCandidate && <Card fluid={true}>
                <Image src={getCandidate.image} wrapped ui={false} size='small' />
                <Card.Content>
                  <Card.Meta>{getCandidate.name}</Card.Meta>
                </Card.Content>
                <Card.Content extra>
                  <Card.Description>Partido: {getCandidate.party}</Card.Description>
                </Card.Content>
              </Card>}
            </Card.Group>
          </Grid.Column>
          <Grid.Column width={5}>
            <Segment>
              <div className='teclado'>
                <div className="teclas">
                  {getButtonsNumbers.map((num) => <button className="tecla" onClick={() => handleCandidateNumber(num.toString())}>{num}</button>)}
                  <ButtonGroup>
                    <div className="opcoes">
                      <button className="btnOpcao branco click" onClick={() => voteWhite()}>BRANCO</button>
                      <button className="btnOpcao laranja click" onClick={() => handleFix()}>CORRIGE</button>
                      <button className="btnOpcao verde" onClick={() => vote()}>CONFIRMA</button>
                    </div>
                  </ButtonGroup>
                </div>
              </div>
            </Segment>

          </Grid.Column>
          <Grid.Column width={4}>
            <p>Resultado da votação:</p>
            <List>
              {getCandidatesVotes.map((x) =>
                <List.Item>
                  <Image avatar src={x.image} />
                  <List.Content>
                    {x.name}
                    <List.Description>
                      {x.party} - {x.votes}
                    </List.Description>
                  </List.Content>
                </List.Item>)}
            </List>
          </Grid.Column>
        </Grid>
      </Container >
    );
  }
}
```

## CSS

Em
```
./src/containers/eleicoes/style.css
```

```css
.teclado{
  text-align: center;
  background-color: #e1e1e1;
  height: 40%;
}

.tecla{
  margin: 4px;
  background-color: black;
  color: snow;
  border-radius: 5px;
  height: 16%;
  width: 27%;
}

.teclas{
  background-color: #444;
  height: 300px;
}
.opcoes{
  font-size: 11px;
  color: black;
}

.branco{
  height: 40px;
  width: 75px;
}
.laranja{
  height: 40px;
  width: 75px;
  background-color: #E67401;
}
.verde{
  height: 50px;
  width: 75px;
  background-color: green;
}
.btnOpcao{
  margin: 2px
}
```

Adicione a store no arquivo do mobx

```
./src/mobx/index.ts
```

Adicione a nova rota `eleicoes`

```ts
import Eleicoes from "../containers/eleicoes";
```

```ts
  { path: `${publicUrl}/eleicoes`, name: 'Eleições', component: Eleicoes, exact: true },
```