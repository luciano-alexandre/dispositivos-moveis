# Encontro 03 - Estrutura de projeto, componentes e JSX

## Visão do encontro

- **Objetivo central:** consolidar a base prática de React Native por meio da leitura da estrutura do projeto, criação de componentes e uso correto de JSX.
- Ao final deste encontro, você deve ser capaz de criar um app inicial com `Expo` **sem Expo Router**, organizar componentes em arquivos separados e montar uma interface simples com código legível.

## Roteiro

1. Retomada rápida do encontro anterior.
2. Criando um projeto com Expo sem Expo Router.
3. Estrutura de pastas e arquivos essenciais.
4. O que é um componente e como quebrar a interface.
5. JSX na prática: regras, boas práticas e erros comuns.
6. Prática 01 guiada.
7. Revisão e exercícios de fixação.

## 1. Revisão do encontro 02

No encontro anterior, você viu a relação entre `React`, `React Native` e `Expo`, além do fluxo básico de execução do app.

Agora a evolução é direta:

- antes: entender o ecossistema;
- agora: organizar código e criar interface de forma estruturada.

## 2. Criando o projeto com Expo sem Expo Router

Neste momento da disciplina, vamos começar com uma estrutura mais simples, baseada em `App.tsx` na raiz do projeto.

### Comandos recomendados

```bash
npx create-expo-app app-mobile --template blank-typescript
cd app-mobile
npm run start
```

### O que esperar da estrutura inicial

```text
app-mobile/
  App.tsx
  app.json
  assets/
  package.json
  tsconfig.json
```

Perceba que esse template não depende da pasta `app/` com rotas por arquivo. Isso reduz complexidade no começo.

### Quando faltar suporte web: instalando dependências

Em alguns ambientes, o projeto inicia normalmente para mobile, mas não consegue abrir no navegador porque faltam dependências web.

Comando para corrigir:

```bash
npx expo install react-dom react-native-web
```

Por que esse comando é importante:

- `react-dom` permite renderizar a aplicação React no navegador;
- `react-native-web` adapta componentes React Native (`View`, `Text`, etc.) para ambiente web.

Se essas dependências não estiverem instaladas, é comum aparecer erro ao tentar rodar no navegador.

### Como testar o app no navegador

Com o projeto criado e dependências instaladas:

```bash
npm run web
```

Alternativa equivalente:

```bash
npx expo start --web
```

Fluxo esperado:

1. o terminal inicia o bundler;
2. uma aba do navegador é aberta (normalmente em `http://localhost:8081` ou porta informada no terminal);
3. o app React Native é renderizado no navegador via React Native Web;
4. ao salvar alterações no código, a página atualiza automaticamente.

Se estiver usando `npm run start`, também é possível pressionar `w` no terminal para abrir a versão web.

## 3. Por que começar sem Expo Router neste início

Escolha para o começo da disciplina:

- menor carga cognitiva: o aluno aprende primeiro componentes, props, estado e estilo;
- ponto de entrada único (`App.tsx`), facilitando leitura;
- menos abstrações no debug inicial;
- separa duas habilidades diferentes: primeiro construir tela, depois navegar entre telas.

Resumo:

- primeiro aprendemos a **construir peças** da UI;
- depois aprendemos a **conectar telas** (navegação).

## 4. Arquivos e pastas essenciais no começo

### `App.tsx`

É o ponto de entrada da aplicação neste estágio inicial.

### `assets/`

Guarda imagens, ícones e outros arquivos estáticos.

### `package.json`

Lista scripts e dependências do projeto.

### `app.json`

Define configurações básicas da aplicação (nome, ícone, identificadores etc.).

## 5. Componente: a unidade de construção da interface

Componente é uma função que retorna interface.

Exemplo mínimo:

```tsx
import { Text } from 'react-native';

export function Saudacao() {
  return <Text>Olá, turma!</Text>;
}
```

No React Native, a interface é construída pela combinação de vários componentes pequenos.

## 6. JSX: como descrevemos a interface

`JSX` é a sintaxe usada para escrever a interface de forma declarativa.

### Regras fundamentais

- retornar um único elemento raiz;
- fechar todas as tags;
- usar `{}` para expressões JavaScript;
- usar componentes customizados com inicial maiúscula.

Exemplo:

```tsx
import { Text, View } from 'react-native';

export default function App() {
  const nome = 'TSI';

  return (
    <View>
      <Text>Turma: {nome}</Text>
    </View>
  );
}
```

### JSX x TSX: qual a diferença

Esse ponto gera dúvida no início, então vale separar bem:

- `JSX` é a sintaxe de interface (`<View>`, `<Text>`, `<Card />` etc.).
- `.jsx` é arquivo JavaScript que pode conter JSX.
- `.tsx` é arquivo TypeScript que pode conter JSX.

Na prática:

- `jsx` = JavaScript + JSX;
- `tsx` = TypeScript + JSX (com tipagem estática).

Como este projeto da disciplina usa TypeScript (`blank-typescript`), os componentes devem ficar em arquivos `.tsx`.

Regra prática:

- use `.tsx` quando o arquivo tiver interface JSX;
- use `.ts` quando não tiver JSX (por exemplo, tipos, funções utilitárias e serviços).

## 7. Diferenças importantes para quem vem da web

### Não existe `div`

Use `View` para estruturação.

### Texto sempre em `Text`

Em React Native, texto não deve ficar solto fora de um componente de texto.

### Estilo não é CSS puro

Usamos objetos JavaScript com `StyleSheet`.

## 8. Exemplo guiado: tela institucional simples

Objetivo: montar uma tela com três partes reutilizáveis (`Cabecalho`, `CardDisciplina`, `Rodape`).

```tsx
import { StyleSheet, Text, View } from 'react-native';

function Cabecalho() {
  return (
    <View style={styles.cabecalho}>
      <Text style={styles.titulo}>Desenvolvimento Mobile</Text>
      <Text style={styles.subtitulo}>Encontro 03</Text>
    </View>
  );
}

function CardDisciplina() {
  return (
    <View style={styles.card}>
      <Text style={styles.cardTitulo}>Tema da aula</Text>
      <Text>Estrutura de projeto, componentes e JSX.</Text>
    </View>
  );
}

function Rodape() {
  return (
    <View style={styles.rodape}>
      <Text>Curso TSI • 2026</Text>
    </View>
  );
}

export default function App() {
  return (
    <View style={styles.container}>
      <Cabecalho />
      <CardDisciplina />
      <Rodape />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    padding: 20,
    gap: 12,
    justifyContent: 'center',
    backgroundColor: '#f5f7fa',
  },
  cabecalho: {
    alignItems: 'center',
    marginBottom: 8,
  },
  titulo: {
    fontSize: 22,
    fontWeight: '700',
  },
  subtitulo: {
    fontSize: 14,
    color: '#4b5563',
  },
  card: {
    backgroundColor: '#ffffff',
    borderRadius: 10,
    padding: 16,
  },
  cardTitulo: {
    fontSize: 16,
    fontWeight: '600',
    marginBottom: 6,
  },
  rodape: {
    alignItems: 'center',
    marginTop: 8,
  },
});
```

## 9. Primeira organização em arquivos separados

Depois de validar no `App.tsx`, evolua para:

```text
app-mobile/
  App.tsx
  src/
    components/
      Cabecalho.tsx
      CardDisciplina.tsx
      Rodape.tsx
```

Essa organização melhora legibilidade e prepara terreno para projetos maiores.

### Conteúdo de cada arquivo separado

`src/components/Cabecalho.tsx`

```tsx
import { StyleSheet, Text, View } from 'react-native';

export function Cabecalho() {
  return (
    <View style={styles.cabecalho}>
      <Text style={styles.titulo}>Desenvolvimento Mobile</Text>
      <Text style={styles.subtitulo}>Encontro 03</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  cabecalho: {
    alignItems: 'center',
    marginBottom: 8,
  },
  titulo: {
    fontSize: 22,
    fontWeight: '700',
  },
  subtitulo: {
    fontSize: 14,
    color: '#4b5563',
  },
});
```

`src/components/CardDisciplina.tsx`

```tsx
import { StyleSheet, Text, View } from 'react-native';

export function CardDisciplina() {
  return (
    <View style={styles.card}>
      <Text style={styles.cardTitulo}>Tema da aula</Text>
      <Text>Estrutura de projeto, componentes e JSX.</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  card: {
    backgroundColor: '#ffffff',
    borderRadius: 10,
    padding: 16,
  },
  cardTitulo: {
    fontSize: 16,
    fontWeight: '600',
    marginBottom: 6,
  },
});
```

`src/components/Rodape.tsx`

```tsx
import { StyleSheet, Text, View } from 'react-native';

export function Rodape() {
  return (
    <View style={styles.rodape}>
      <Text>Curso TSI • 2026</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  rodape: {
    alignItems: 'center',
    marginTop: 8,
  },
});
```

### Como importar no `App.tsx`

`App.tsx`

```tsx
import { StyleSheet, View } from 'react-native';
import { Cabecalho } from './src/components/Cabecalho';
import { CardDisciplina } from './src/components/CardDisciplina';
import { Rodape } from './src/components/Rodape';

export default function App() {
  return (
    <View style={styles.container}>
      <Cabecalho />
      <CardDisciplina />
      <Rodape />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    padding: 20,
    gap: 12,
    justifyContent: 'center',
    backgroundColor: '#f5f7fa',
  },
});
```

Com esse padrão, cada componente cuida da sua responsabilidade, e o `App.tsx` fica mais limpo para orquestrar a tela.

## 10. Erros comuns neste encontro

### Esquecer `export`

Sem export, o componente não pode ser importado em outro arquivo.

### Usar minúscula em componente customizado

`<cabecalho />` está errado; o correto é `<Cabecalho />`.

### Misturar muita lógica na interface

No começo, mantenha foco em estrutura visual limpa e legível.

### Começar com navegação avançada cedo demais

Antes de rotas, garanta domínio de componentes e JSX.

## 11. Prática 01 (entrega do encontro)

### Objetivo

Criar uma tela de **painel do estudante** com componentes separados.

### Requisitos mínimos

1. Projeto criado com Expo sem Expo Router.
2. Criar componente `BoasVindas` com nome do aluno e curso.
3. Criar componente `CardProximaAula` com título da próxima aula e uma descrição curta.
4. Criar componente `CardPendencia` com ao menos 2 pendências em formato de texto.
5. Criar componente `Rodape` com semestre ou turma.
6. Organizar os componentes em `src/components/`.
7. Estilização básica com `StyleSheet`.

### Critérios de avaliação

- organização de arquivos;
- legibilidade do código;
- uso correto de JSX;
- separação correta entre componentes;
- execução do app sem erros.

## 12. Checklist de validação do aluno

- o projeto inicia com `npm run start`;
- se necessário, as dependências web foram instaladas com `npx expo install react-dom react-native-web`;
- o app abre no navegador com `npm run web` (ou tecla `w` no terminal);
- a tela aparece no dispositivo/emulador;
- os componentes da prática são renderizados corretamente;
- não há erro de importação ou exportação;
- o código está organizado em pastas.

## 13. Exercícios de revisão

1. Qual é a diferença entre usar `App.tsx` como entrada única e usar rotas por arquivos?
2. Por que começamos sem Expo Router neste momento da disciplina?
3. O que caracteriza um componente em React Native?
4. Quais são as regras básicas de JSX que mais causam erro no início?
5. Por que separar a interface em componentes menores?

## 14. Exercícios de estudo

- Reescreva a tela da prática trocando os textos por dados do seu perfil acadêmico.
- Adicione um card extra chamado `CardMetaDaSemana`.
- Crie uma variação de estilo para o componente `BoasVindas` (cor, espaçamento e fonte).
- Explique, em 5 linhas, por que a abordagem sem Router é melhor para o início.

## 15. Resumo do encontro

Neste encontro, você avançou da visão geral para a construção real da interface. O foco foi criar projeto com Expo sem Router para reduzir complexidade inicial, entender melhor o papel do `App.tsx`, praticar componentização e usar JSX com segurança. Essa base é essencial para os próximos encontros, quando entraremos em `props`, estado, eventos e, depois, navegação entre telas.

## Materiais complementares

- React Native docs (Core Components): <https://reactnative.dev/docs/components-and-apis>
- React docs (Writing Markup with JSX): <https://react.dev/learn/writing-markup-with-jsx>
- Expo docs (create-expo-app): <https://docs.expo.dev/more/create-expo/>
