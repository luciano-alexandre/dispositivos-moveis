# Encontro 03 - Estrutura de projeto, componentes e JSX

## Foco do encontro

- Ler a organização de um projeto React Native.
- Introduzir componentes funcionais.
- Explicar JSX como sintaxe declarativa.

## Roteiro de 90 minutos

1. Tour pelas pastas do projeto.
2. Conceito de componente.
3. JSX, interpolação e fragmentação da UI.
4. Separação entre apresentação e dados.
5. Prática 01.

## Explicação técnica

Componentes são funções que retornam uma descrição da interface. JSX parece HTML, mas é JavaScript com açúcar sintático. O ponto pedagógico aqui é mostrar que a interface não é montada manualmente linha por linha; ela é derivada do estado atual da aplicação.

```tsx
type SaudacaoProps = {
  nome: string;
};

function Saudacao({ nome }: SaudacaoProps) {
  return <Text>Olá, {nome}!</Text>;
}

export default function App() {
  return (
    <View>
      <Saudacao nome="Turma TSI" />
    </View>
  );
}
```

## Pontos de atenção

- Todo componente precisa retornar um único nó raiz.
- Expressões JavaScript ficam entre chaves.
- Componentes customizados começam com letra maiúscula.

## Atividade prática

- Criar componentes `Cabecalho`, `Card` e `Rodape`.
- Montar uma tela institucional simples da disciplina.

## Materiais complementares

- JSX in depth: <https://react.dev/learn/writing-markup-with-jsx>
- Your first component: <https://react.dev/learn/your-first-component>
