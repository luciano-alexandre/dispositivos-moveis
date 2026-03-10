# Encontro 12 - Parâmetros entre telas e fluxo multijanelas

## Objetivos

- Enviar dados entre telas.
- Ler parâmetros de rota com segurança.
- Modelar fluxos simples de listagem, detalhe e edição.

## Explicação didática

Navegar sem contexto gera telas isoladas. Navegar com parâmetros permite que a interface carregue um item específico, personalize o título da tela ou decida qual ação deve ser executada. É importante explicar a diferença entre passar um identificador e passar o objeto completo.

```tsx
navigation.navigate('Detalhe', { id: item.id, titulo: item.titulo });
```

```tsx
function DetalheScreen({ route }) {
  const { id, titulo } = route.params;
  return <Text>{id} - {titulo}</Text>;
}
```

## Atividade

- Criar uma lista de produtos.
- Ao tocar em um item, abrir tela de detalhe.
- Preparar botão para editar o item selecionado.

## Materiais complementares

- Params em rotas: <https://reactnavigation.org/docs/params>
- Passing data deeply: <https://react.dev/learn/passing-data-deeply-with-context>
