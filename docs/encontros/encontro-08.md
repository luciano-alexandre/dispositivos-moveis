# Encontro 08 - Correção passo a passo da atividade avaliativa 01

## Visão do encontro

- **Objetivo central:** revisar a atividade avaliativa de forma guiada, reconstruindo uma solução coerente para o app **Vistoria Rápida** e destacando as decisões técnicas mais importantes.
- Ao final deste encontro, você deve ser capaz de organizar um app móvel em componentes reutilizáveis, controlar um formulário completo com `useState`, validar regras condicionais e apresentar feedback claro ao usuário.

## Roteiro

1. Revisão dos requisitos do enunciado.
2. Estrutura esperada do projeto.
3. Modelagem de estado e tipos.
4. Componentização da interface.
5. Validação do formulário.
6. Resumo em tempo real e situação geral.
7. Ações de registrar e limpar.
8. Ajustes visuais e checklist final.

## 1. Revisão dos requisitos do enunciado

Antes de olhar para a implementação, vale separar o problema em blocos:

- cabeçalho e rodapé;
- formulário controlado;
- seletor de condição;
- resumo em tempo real;
- validação com regras obrigatórias;
- mensagem de sucesso;
- limpeza completa da tela.

Essa leitura evita começar pelo código sem entender o fluxo da interface.

## 2. Estrutura esperada do projeto

Uma organização simples e suficiente para a atividade:

```text
App.tsx
src/
  components/
    CabecalhoApp.tsx
    CampoFormulario.tsx
    SeletorCondicao.tsx
    ResumoVistoria.tsx
    BotaoAcao.tsx
```

Pontos-chave:

- `App.tsx` concentra o estado principal da tela;
- componentes em `src/components/` recebem dados por `props`;
- a solução continua simples, mas já mostra separação de responsabilidades.

## 3. Modelagem de estado e tipos

Uma modelagem clara facilita toda a correção:

```tsx
type CondicaoEntrega = 'integra' | 'avaria';

type FormData = {
  responsavel: string;
  email: string;
  codigoPedido: string;
  bairroEntrega: string;
  quantidadeVolumes: string;
  observacao: string;
};

type FormErrors = Partial<Record<keyof FormData, string>>;

type ResumoRegistrado = {
  responsavel: string;
  codigoPedido: string;
  quantidadeVolumes: string;
  condicao: CondicaoEntrega;
};
```

Estados principais esperados:

- `form`: guarda os campos digitados;
- `erros`: guarda mensagens por campo;
- `condicao`: controla se a entrega está íntegra ou com avaria;
- `resumoRegistrado`: armazena o último envio válido.

## 4. Componentização da interface

Uma divisão possível para a correção:

- `CabecalhoApp`: título do app e frase de apoio;
- `CampoFormulario`: rótulo, `TextInput`, erro e destaque visual;
- `SeletorCondicao`: botões por toque para alternar a condição;
- `ResumoVistoria`: mostra dados principais e situação geral;
- `BotaoAcao`: botão reutilizável para registrar e limpar.

Exemplo de `props` tipadas para um campo:

```tsx
type CampoFormularioProps = {
  label: string;
  value: string;
  placeholder: string;
  error?: string;
  keyboardType?: 'default' | 'email-address' | 'numeric';
  multiline?: boolean;
  onChangeText: (value: string) => void;
};
```

A ideia central é deixar `App.tsx` responsável pela regra e os componentes responsáveis pela apresentação.

## 5. Validação do formulário

O momento da validação é o toque em `Registrar vistoria`.

```tsx
function validar(dados: FormData, condicao: CondicaoEntrega): FormErrors {
  const erros: FormErrors = {};

  if (!dados.responsavel.trim() || dados.responsavel.trim().length < 3) {
    erros.responsavel = 'Informe um responsavel com pelo menos 3 caracteres.';
  }

  if (!dados.email.includes('@') || !dados.email.includes('.')) {
    erros.email = 'Informe um e-mail valido.';
  }

  if (!dados.codigoPedido.trim() || dados.codigoPedido.trim().length < 5) {
    erros.codigoPedido = 'Informe um codigo de pedido com pelo menos 5 caracteres.';
  }

  if (!dados.bairroEntrega.trim()) {
    erros.bairroEntrega = 'Informe o bairro de entrega.';
  }

  const volumes = Number(dados.quantidadeVolumes);
  if (!dados.quantidadeVolumes || Number.isNaN(volumes) || volumes <= 0) {
    erros.quantidadeVolumes = 'Informe uma quantidade de volumes maior que zero.';
  }

  if (condicao === 'avaria' && dados.observacao.trim().length < 10) {
    erros.observacao = 'Descreva a avaria com pelo menos 10 caracteres.';
  }

  return erros;
}
```

Decisões importantes:

- usar `trim()` para ignorar espaços vazios;
- validar número com `Number(...)`;
- aplicar regra condicional na observação;
- devolver um objeto de erros por campo.

## 6. Resumo em tempo real e situação geral

O resumo não depende de envio: ele pode ser calculado enquanto o usuário digita.

Uma função útil:

```tsx
function obterSituacaoGeral(form: FormData, erros: FormErrors) {
  const camposEssenciais =
    form.responsavel &&
    form.codigoPedido &&
    form.quantidadeVolumes;

  if (!camposEssenciais) {
    return 'Aguardando preenchimento';
  }

  if (Object.keys(erros).length > 0) {
    return 'Revisar dados';
  }

  return 'Vistoria pronta para envio';
}
```

Esse painel deve mostrar:

- responsável;
- código do pedido;
- quantidade de volumes;
- condição selecionada;
- situação geral.

## 7. Ações de registrar e limpar

Fluxo esperado de `Registrar vistoria`:

1. validar os dados;
2. salvar erros encontrados;
3. interromper envio se houver erro;
4. mostrar confirmação em caso de sucesso;
5. manter um resumo da vistoria registrada.

Fluxo esperado de `Limpar`:

1. resetar todos os campos;
2. limpar mensagens de erro;
3. voltar a condição inicial;
4. remover o resumo registrado.

Exemplo de reset:

```tsx
const initialForm: FormData = {
  responsavel: '',
  email: '',
  codigoPedido: '',
  bairroEntrega: '',
  quantidadeVolumes: '',
  observacao: '',
};
```

## 8. Ajustes visuais importantes

Na correção, vale observar:

- uso de `Flexbox` para organizar blocos;
- espaçamento consistente entre campos;
- destaque visual em campos com erro;
- contraste adequado em botões e mensagens;
- legibilidade do resumo em tela pequena.

## 9. Checklist da solução corrigida

- projeto criado com Expo `blank-typescript`;
- `App.tsx` como ponto de entrada;
- pelo menos quatro componentes reutilizáveis;
- `props` tipadas nos componentes;
- formulário totalmente controlado;
- validação cobrindo todas as regras do enunciado;
- feedback visual coerente;
- resumo atualizado em tempo real;
- registro com sucesso funcionando;
- limpeza completa da interface funcionando.

## 10. Aprendizados centrais da correção

- decompor o problema em partes reduz a complexidade da implementação;
- centralizar o estado em `App.tsx` facilita a coordenação da tela;
- componentes pequenos deixam o código mais legível e reaproveitável;
- validação precisa refletir exatamente o enunciado da atividade;
- feedback visual é parte da solução, não apenas acabamento.

## Encerramento

Depois da correção da atividade avaliativa, o próximo passo é retomar a trilha de conteúdo com o tema **formulários controlados e máscaras**, que passa a compor o encontro 09.
