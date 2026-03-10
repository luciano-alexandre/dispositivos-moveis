# Encontro 31 - Debugging e observabilidade

## Objetivos

- Investigar erros de interface, estado e integração.
- Usar logs, inspeção e ferramentas de depuração.
- Introduzir observabilidade básica.

## Explicação técnica

Depurar é formular hipótese, coletar evidência e testar correção. É importante abandonar a ideia de tentativa aleatória. Logs estruturados, inspeção de requisições e leitura de stack trace fazem parte do método.

```tsx
console.log('estado atual', { carregando, erro, quantidade: itens.length });
```

## Ferramentas para demonstrar

- console e logs do Metro;
- inspeção de rede;
- React DevTools;
- monitoramento de erros em produção.

## Atividade

- Corrigir um app com falhas previamente inseridas.
- Documentar o caminho de investigação usado pela equipe.

## Materiais complementares

- React Native debugging: <https://reactnative.dev/docs/debugging>
- Sentry for React Native: <https://docs.sentry.io/platforms/react-native/>
