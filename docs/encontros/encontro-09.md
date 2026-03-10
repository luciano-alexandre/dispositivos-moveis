# Encontro 09 - Componentização e reutilização de UI

## Objetivos de aprendizagem

- Identificar duplicação de interface.
- Extrair componentes reutilizáveis.
- Projetar APIs simples para componentes.

## Explicação didática

Componentizar não é quebrar a tela em pedaços arbitrários. É criar unidades reutilizáveis, legíveis e previsíveis. Um bom componente tem responsabilidade clara, recebe poucas `props` e preserva coerência visual.

```tsx
type CardProps = {
  titulo: string;
  descricao: string;
  destaque?: boolean;
};

export function Card({ titulo, descricao, destaque = false }: CardProps) {
  return (
    <View style={[styles.card, destaque && styles.cardDestaque]}>
      <Text style={styles.titulo}>{titulo}</Text>
      <Text>{descricao}</Text>
    </View>
  );
}
```

## Critérios para discussão

- O componente é genérico o suficiente para reuso?
- Os nomes das `props` são autoexplicativos?
- O componente mistura regra de negócio com apresentação?

## Atividade

- Refatorar uma tela construída nos encontros anteriores.
- Criar uma pequena biblioteca local de componentes reutilizáveis.

## Materiais complementares

- Thinking in React: <https://react.dev/learn/thinking-in-react>
- Composition vs inheritance: <https://legacy.reactjs.org/docs/composition-vs-inheritance.html>
