# Encontro 07 - Formulários controlados e máscaras

## Metas do encontro

- Consolidar o conceito de componente controlado.
- Aplicar formatação de dados de entrada.
- Discutir usabilidade em formulários longos.

## Explicação técnica

Um formulário controlado é aquele em que o valor exibido pelo campo depende do estado do componente. Isso permite validação, máscara, normalização e persistência parcial. O custo é maior necessidade de organização do código.

```tsx
function formatarTelefone(valor: string) {
  const numeros = valor.replace(/\D/g, '').slice(0, 11);
  return numeros
    .replace(/^(\d{2})(\d)/, '($1) $2')
    .replace(/(\d{5})(\d)/, '$1-$2');
}
```

```tsx
<TextInput
  value={telefone}
  onChangeText={(texto) => setTelefone(formatarTelefone(texto))}
  keyboardType="phone-pad"
/>
```

## Estratégia pedagógica

- Mostrar primeiro um campo sem máscara.
- Demonstrar a melhoria de legibilidade com máscara.
- Debater quando a máscara ajuda e quando atrapalha.

## Atividade

- Criar formulário com telefone e CPF.
- Separar funções de formatação em um arquivo utilitário.

## Materiais complementares

- Controlled components: <https://react.dev/learn/sharing-state-between-components>
- Boas práticas de UX para formulários móveis: <https://material.io/components/text-fields>
