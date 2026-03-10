# Encontro 16 - AsyncStorage: armazenamento chave-valor

## Objetivos

- Salvar e recuperar dados simples localmente.
- Serializar objetos com JSON.
- Tratar carregamento assíncrono.

## Explicação técnica

AsyncStorage funciona como um armazenamento persistente baseado em chave e valor. É adequado para preferências, rascunhos leves e flags de configuração. Como os valores são string, objetos precisam ser serializados com `JSON.stringify`.

```tsx
import AsyncStorage from '@react-native-async-storage/async-storage';

async function salvarTema(tema: string) {
  await AsyncStorage.setItem('tema', tema);
}

async function lerTema() {
  return AsyncStorage.getItem('tema');
}
```

## Atenção pedagógica

- Reforçar `async/await`.
- Mostrar o impacto de ler dados no carregamento inicial.
- Evitar salvar estruturas grandes sem critério.

## Atividade

- Persistir tema, nome do usuário e último acesso.
- Restaurar os dados ao abrir o app.

## Materiais complementares

- AsyncStorage docs: <https://react-native-async-storage.github.io/async-storage/docs/usage/>
