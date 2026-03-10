# Encontro 30 - Testes em app móvel (unitário e componente)

## Objetivos

- Introduzir testes automatizados.
- Diferenciar teste unitário, de componente e de integração.
- Criar testes para lógica e interface.

## Explicação técnica

Teste automatizado reduz regressão e melhora confiança para refatorar. Na disciplina, o foco deve ser pragmático: testar comportamentos importantes, sem transformar a turma em especialista em infraestrutura de teste neste momento.

```tsx
import { render, screen } from '@testing-library/react-native';

test('exibe mensagem inicial', () => {
  render(<Text>Olá turma</Text>);
  expect(screen.getByText('Olá turma')).toBeTruthy();
});
```

## Estratégia pedagógica

- Começar por função pura.
- Evoluir para componente simples.
- Mostrar como nomear testes pelo comportamento esperado.

## Atividade

- Escrever testes para um formulário ou lista.
- Validar renderização condicional e interação básica.

## Materiais complementares

- Testing Library React Native: <https://callstack.github.io/react-native-testing-library/>
- Jest docs: <https://jestjs.io/docs/getting-started>
