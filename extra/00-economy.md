## Ajustando APIS

> economy.api.ts

```tsx
import axios from 'axios';
import { configs } from '../configs';

export const getPrice = () => {
  return axios.request({ url: configs.apis.economia });
}
```