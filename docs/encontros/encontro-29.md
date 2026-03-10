# Encontro 29 - Performance móvel: renderização e memoização

## Objetivos

- Entender causas comuns de lentidão.
- Avaliar renderizações desnecessárias.
- Aplicar memoização com critério.

## Explicação técnica

Performance em apps móveis está ligada a renderização, serialização, listas extensas, imagens pesadas e trabalho excessivo na thread principal. É importante ensinar que memoização não é remédio universal; ela só ajuda quando há custo mensurável e estabilidade de referências.

```tsx
const Item = memo(function Item({ titulo }: { titulo: string }) {
  return <Text>{titulo}</Text>;
});
```

## Pontos de análise

- listas grandes;
- estados muito amplos;
- re-render em cascata;
- imagens sem otimização.

## Atividade

- Medir comportamento de uma lista antes e depois de ajustes.
- Identificar otimizações úteis e prematuras.

## Materiais complementares

- React Native performance: <https://reactnative.dev/docs/performance>
- React memo: <https://react.dev/reference/react/memo>
