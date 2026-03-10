# Encontro 30 - Testes em app móvel (unitário e componente)

## Objetivos

- Introduzir testes automatizados.
- Diferenciar teste unitário, de componente e de integração.
- Criar testes para lógica e interface.

## Explicação técnica

Teste automatizado reduz regressão e melhora confiança para refatorar. O foco deve ser pragmático: testar comportamentos importantes, sem tentar dominar toda a infraestrutura de testes de uma só vez.

```tsx
import { render, screen } from '@testing-library/react-native';

test('exibe mensagem inicial', () => {
  render(<Text>Olá mundo</Text>);
  expect(screen.getByText('Olá mundo')).toBeTruthy();
});
```

## Como estudar este tema

- Começar por função pura.
- Evoluir para componente simples.
- Mostrar como nomear testes pelo comportamento esperado.

## Atividade

- Escrever testes para um formulário ou lista.
- Validar renderização condicional e interação básica.

## Materiais complementares

- Testing Library React Native: <https://callstack.github.io/react-native-testing-library/>
- Jest docs: <https://jestjs.io/docs/getting-started>
