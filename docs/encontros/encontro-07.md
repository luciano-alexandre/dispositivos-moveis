# Encontro 07 - Atividade avaliativa 01

## Visão do encontro

- **Objetivo central:** realizar a primeira atividade avaliativa prática da disciplina, consolidando os conteúdos de componentes, props, estado, eventos, layout com Flexbox e formulários controlados.
- **Formato:** atividade individual com implementação em React Native usando Expo, em projeto sem Expo Router e com solução em tela única.
- **Material principal da atividade:** [PDF da atividade avaliativa](../../provas/atividade-avaliativa-encontro-07.pdf).

## Organização da aula

1. Leitura orientada do enunciado.
2. Revisão rápida dos critérios obrigatórios.
3. Implementação individual da atividade.
4. Testes, ajustes finais e revisão da entrega.
5. Encerramento com checklist de conferência.

## Tema da atividade

Nesta avaliação, você irá desenvolver a primeira versão do aplicativo **Vistoria Rápida**, usado para registrar em campo se um pedido chegou em boas condições.

O problema foi proposto para simular um cenário real de uso móvel:

- tela pequena;
- preenchimento rápido;
- interação por toque;
- feedback visual claro para o usuário.

## Enunciado oficial

O enunciado completo está no arquivo:

- [Atividade Avaliativa - Dispositivos móveis.pdf](../../provas/atividade-avaliativa-encontro-07.pdf)

## Escopo esperado da solução

Sua tela deve conter:

1. cabeçalho com nome do aplicativo e frase curta de apoio;
2. formulário controlado com os campos `responsavel`, `email`, `codigoPedido`, `bairroEntrega`, `quantidadeVolumes` e `observacao`;
3. seletor por toque para a condição da entrega:
   - `Entrega íntegra`;
   - `Entrega com avaria`;
4. painel de resumo atualizado em tempo real;
5. botão `Registrar vistoria`;
6. botão `Limpar`;
7. rodapé com nome e matrícula do aluno.

## Requisitos obrigatórios

- criar o projeto com Expo usando o template `blank-typescript`;
- manter `App.tsx` como ponto de entrada principal;
- organizar componentes em `src/components/`;
- criar pelo menos quatro componentes reutilizáveis com `props` tipadas;
- controlar todos os campos com `useState`;
- validar o formulário no toque em `Registrar vistoria`;
- exibir mensagens de erro abaixo dos campos inválidos;
- destacar visualmente os campos com erro;
- mostrar confirmação ao registrar com sucesso;
- manter na tela um resumo da vistoria registrada;
- limpar formulário, erros, condição e resumo ao tocar em `Limpar`;
- construir o layout com Flexbox.

## Regras de validação

Verifique obrigatoriamente:

- `responsavel`: pelo menos 3 caracteres;
- `email`: deve conter `@` e `.`;
- `codigoPedido`: obrigatório com pelo menos 5 caracteres;
- `bairroEntrega`: obrigatório;
- `quantidadeVolumes`: número maior que zero;
- `observacao`: obrigatória apenas quando a condição for `Entrega com avaria`, com pelo menos 10 caracteres.

## Sequência sugerida de desenvolvimento

1. Criar o projeto e preparar a estrutura básica em `App.tsx` e `src/components/`.
2. Montar o cabeçalho e o rodapé.
3. Criar os campos controlados do formulário.
4. Implementar o seletor da condição da entrega.
5. Exibir o painel de resumo em tempo real.
6. Adicionar as funções de validação.
7. Implementar os botões `Registrar vistoria` e `Limpar`.
8. Refinar o layout e revisar mensagens de erro.

## Critérios observáveis na correção

- organização e legibilidade do código;
- uso correto de componentes com `props` tipadas;
- controle de estado dos campos;
- coerência das validações;
- qualidade do feedback visual;
- funcionamento dos botões e do resumo;
- aderência ao enunciado do PDF.

## Checklist antes de entregar

- o app abre sem erro no ambiente Expo;
- todos os campos editáveis respondem corretamente;
- o seletor de condição funciona por toque;
- os erros aparecem no campo certo;
- a observação só é exigida em caso de avaria;
- o resumo muda conforme o preenchimento;
- o registro com sucesso mostra confirmação;
- o botão `Limpar` realmente reinicia a tela;
- nome e matrícula aparecem no rodapé.

## Encerramento

Este encontro é dedicado integralmente à execução da atividade avaliativa. No encontro 08, a proposta será retomada em uma **correção passo a passo**, com revisão da estrutura do projeto, da modelagem do estado, das validações e da organização da interface.
