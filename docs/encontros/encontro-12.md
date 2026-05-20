# Encontro 12 - Parâmetros entre telas e fluxo multijanelas

## Visão do encontro

- **Objetivo central:** evoluir o app do encontro 11 (tabs + drawer) para um fluxo multijanelas com troca de dados por parâmetros tipados no React Navigation.
- Ao final deste encontro, você deve ser capaz de enviar e ler parâmetros com segurança, definir parâmetros opcionais, atualizar `route.params` e manter um fluxo coerente dentro da navegação já existente.

## Roteiro

1. Retomada dos encontros 10 e 11.
2. O que são parâmetros de rota e por que usar.
3. Tipagem de parâmetros com TypeScript.
4. Enviando parâmetros com `navigate` e `push`.
5. Lendo parâmetros com `route.params`.
6. Parâmetros opcionais, `initialParams` e `setParams`.
7. Tutorial prático: evolução do app do encontro 11.
8. Estratégias para retorno de dados no fluxo.
9. Exercício guiado.
10. Revisão e exercícios de fixação.

## 1. Retomada dos encontros 10 e 11

Progressão até aqui:

- encontro 10: navegação sequencial com `stack`;
- encontro 11: navegação estrutural com `Bottom Tabs` + `Drawer`.

Agora vamos combinar as duas ideias no mesmo projeto: manter `tabs + drawer` como estrutura principal e criar um fluxo em pilha dentro da aba **Atendimentos** para passar dados entre telas.

## 2. O que são parâmetros de rota e por que usar

Parâmetros de rota são dados enviados no momento da navegação. Exemplo:

```tsx
navigation.navigate('DetalhesAtendimento', {
  chamadoId: 'CH-103',
  cliente: 'Ana Souza',
});
```

### Leitura do código

1. O primeiro argumento (`'DetalhesAtendimento'`) é o nome da rota de destino.
2. O segundo argumento é o objeto de dados que a próxima tela vai consumir.

Uso recomendado:

- identificar o que a próxima tela deve mostrar (`id`, `filtro`, `modo`);
- carregar dados específicos por identificador;
- manter fluxo explícito e previsível.

Boa prática:

- prefira dados pequenos e serializáveis;
- passe identificadores em vez de objetos grandes;
- mantenha consistência de nomes entre origem e destino.

## 3. Tipagem de parâmetros com TypeScript

No fluxo de atendimentos, defina uma lista de rotas tipada:

```tsx
export type AtendimentosStackParamList = {
  AtendimentosLista:
    | {
        ultimoChamadoId?: string;
        ultimaAcao?: 'concluido' | 'pendente';
      }
    | undefined;
  DetalhesAtendimento: {
    chamadoId: string;
    cliente: string;
    prioridade?: 'alta' | 'normal';
  };
  FinalizacaoAtendimento: {
    chamadoId: string;
    tecnico: string;
    status: 'concluido' | 'pendente';
    observacao?: string;
  };
};
```

### Leitura do código

1. `AtendimentosLista` aceita retorno opcional do fechamento.
2. `DetalhesAtendimento` exige `chamadoId` e `cliente`.
3. `FinalizacaoAtendimento` exige dados finais do processo.
4. Campos opcionais (`?`) dão flexibilidade sem perder contrato.

## 4. Enviando parâmetros com `navigate` e `push`

Exemplos no mesmo fluxo:

```tsx
navigation.navigate('DetalhesAtendimento', {
  chamadoId: 'CH-204',
  cliente: 'Carlos Menezes',
  prioridade: 'alta',
});

navigation.push('DetalhesAtendimento', {
  chamadoId: 'CH-205',
  cliente: 'Marina F.',
});
```

### Leitura do código

1. `navigate` tenta reutilizar rota existente.
2. `push` sempre cria nova instância da tela na pilha.

## 5. Lendo parâmetros com `route.params`

Dentro da tela de detalhes:

```tsx
import type { NativeStackScreenProps } from '@react-navigation/native-stack';
import type { AtendimentosStackParamList } from '../navigation/AtendimentosStack';

type Props = NativeStackScreenProps<AtendimentosStackParamList, 'DetalhesAtendimento'>;

export function DetalhesAtendimentoScreen({ route }: Props) {
  const { chamadoId, cliente, prioridade } = route.params;

  return (
    <>
      {/* renderização */}
    </>
  );
}
```

### Leitura do código

1. `NativeStackScreenProps` tipa `navigation` e `route`.
2. `route.params` fica com contrato explícito, sem `any`.

## 6. Parâmetros opcionais, `initialParams` e `setParams`

Definindo valor padrão da rota:

```tsx
<Stack.Screen
  name="DetalhesAtendimento"
  component={DetalhesAtendimentoScreen}
  initialParams={{ prioridade: 'normal' }}
/>
```

Atualizando parâmetros da tela atual:

```tsx
navigation.setParams({ prioridade: 'alta' });
```

### Leitura do código

1. `initialParams` cobre abertura sem parâmetro opcional.
2. `setParams` faz merge dos parâmetros atuais sem recriar a tela.

## 7. Tutorial prático: evolua sozinho o app do encontro 11

Neste tópico, você **não vai copiar um bloco pronto**. A ideia é evoluir, por conta própria, o projeto final do encontro 11 e adicionar os conceitos novos deste encontro.

### Regra do tutorial

- mantenha `tabs + drawer` como base;
- implemente um `stack` somente dentro da aba `Atendimentos`;
- use tipagem, `navigate`, `push`, `route.params`, `initialParams` e `setParams`;
- ao final, a lista de atendimentos deve receber um resumo da última operação.

### Estrutura-alvo (para você montar)

```text
src/
  navigation/
    RootNavigator.tsx        (já existe do encontro 11)
    AppDrawer.tsx            (já existe do encontro 11)
    AppTabs.tsx              (ajustar)
    AtendimentosStack.tsx    (criar)
  screens/
    DashboardScreen.tsx      (já existe do encontro 11)
    AtendimentosListaScreen.tsx         (criar)
    DetalhesAtendimentoScreen.tsx       (criar)
    FinalizacaoAtendimentoScreen.tsx    (criar)
    RelatoriosScreen.tsx     (já existe do encontro 11)
    ConfiguracoesScreen.tsx  (já existe do encontro 11)
  styles.ts                  (reaproveitar e ampliar)
```

### Passo 1 - Criar o `AtendimentosStack` com tipagem

Crie `src/navigation/AtendimentosStack.tsx` e implemente:

1. tipo `AtendimentosStackParamList` com três rotas: `AtendimentosLista` (params opcionais de retorno), `DetalhesAtendimento` (exige `chamadoId` e `cliente`) e `FinalizacaoAtendimento` (exige `chamadoId`, `tecnico` e `status`).
2. `createNativeStackNavigator<AtendimentosStackParamList>()`.
3. `initialParams` em `DetalhesAtendimento` com `prioridade: 'normal'`.

Checklist do passo:

- o arquivo compila sem `any`;
- as três rotas estão registradas no stack.

### Passo 2 - Ligar o stack na aba `Atendimentos`

No `AppTabs.tsx` do encontro 11:

1. importe `NavigatorScreenParams` e o novo `AtendimentosStack`;
2. troque o tipo da aba `Atendimentos` para `NavigatorScreenParams<AtendimentosStackParamList> | undefined`;
3. use `component={AtendimentosStack}` na aba `Atendimentos`;
4. adicione `headerShown: false` nessa aba.

Checkpoint:

- abrir a aba `Atendimentos` deve mostrar a primeira tela do stack, sem cabeçalho duplicado.

### Passo 3 - Montar `AtendimentosListaScreen`

Crie uma lista de chamados e implemente:

1. tipagem com `NativeStackScreenProps<AtendimentosStackParamList, 'AtendimentosLista'>`;
2. leitura de `route.params?.ultimoChamadoId` e `route.params?.ultimaAcao`;
3. botão/item que faz `navigate('DetalhesAtendimento', { chamadoId, cliente, prioridade })`.

Checkpoint:

- tocar em um chamado abre detalhes com parâmetros corretos.

### Passo 4 - Montar `DetalhesAtendimentoScreen`

Implemente a tela intermediária:

1. leia `chamadoId`, `cliente` e `prioridade` de `route.params`;
2. crie estado local para `tecnico` e `status`;
3. valide técnico antes de avançar;
4. faça `navigate('FinalizacaoAtendimento', ...)` enviando dados finais;
5. adicione um botão com `setParams({ prioridade: 'alta' })`;
6. adicione uma ação de teste com `push('DetalhesAtendimento', ...)`.

Checkpoint:

- `setParams` atualiza a prioridade exibida;
- `push` cria nova instância da mesma tela.

### Passo 5 - Montar `FinalizacaoAtendimentoScreen`

Implemente a tela final:

1. tipagem com `NativeStackScreenProps<AtendimentosStackParamList, 'FinalizacaoAtendimento'>`;
2. exibição do resumo usando `route.params`;
3. botão para `navigate('AtendimentosLista', { ultimoChamadoId, ultimaAcao })`.

Checkpoint:

- ao concluir, a lista exibe o resumo da última operação.

### Passo 6 - Ajustar estilos sem quebrar o encontro 11

No `styles.ts`, adicione apenas o necessário para:

- lista de chamados (`item`, `itemTitulo`, `itemSubtitulo`);
- alerta de retorno (`alerta`, `alertaTexto`);
- input e variações de botões (`input`, `linhaBotoes`, etc.).

Mantenha a paleta e padrão visual já usados no encontro 11.

### Passo 7 - Teste guiado (obrigatório)

Execute este roteiro manual:

1. abra `Atendimentos` e selecione um chamado;
2. confira se `chamadoId` e `cliente` aparecem corretamente;
3. use `setParams` e valide mudança da prioridade em tela;
4. avance para finalização e confirme os dados;
5. conclua e verifique o retorno na lista com `ultimoChamadoId`;
6. use a ação com `push` e confira múltiplas telas no histórico de navegação.

Se algum teste falhar, volte ao passo correspondente e corrija antes de seguir.

### Resultado esperado do tutorial

No final, seu app deve continuar com `tabs + drawer`, mas com fluxo de atendimento em três telas tipadas, passagem de parâmetros em ida e volta e uso explícito de `navigate`, `push`, `route.params`, `initialParams` e `setParams`.

## 8. Estratégias para retorno de dados no fluxo

Duas estratégias úteis neste momento:

1. `goBack()` quando não há dado para retornar;
2. `navigate('AtendimentosLista', params)` quando você precisa atualizar contexto da tela anterior.

Assim, você evita estado global quando o dado pertence somente a esse fluxo.

## 9. Exercício guiado

### Objetivo

Evoluir o app **Central de Campo** do encontro 11, tornando a aba `Atendimentos` um fluxo com três telas e parâmetros tipados.

### Requisitos mínimos

1. Manter `tabs + drawer` funcionando como no encontro 11.
2. Criar `AtendimentosStack` com `AtendimentosLista`, `DetalhesAtendimento` e `FinalizacaoAtendimento`.
3. Usar `navigate` com parâmetros obrigatórios tipados.
4. Demonstrar `push` em ao menos uma ação da tela de detalhes.
5. Usar `initialParams` em campo opcional da rota.
6. Atualizar um parâmetro da rota atual com `setParams`.
7. Retornar para lista com resumo da última operação.

### Entrega esperada

- fluxo completo sem navegação quebrada;
- parâmetros chegando corretamente em todas as telas;
- tipagem acusando erro ao remover campo obrigatório;
- estrutura de arquivos organizada por `navigation/`, `screens/` e `styles.ts`.

## 10. Checklist de validação do aluno

- `AtendimentosStackParamList` está definido e coerente;
- telas do fluxo usam `NativeStackScreenProps` corretamente;
- `navigate`/`push` enviam dados esperados;
- `route.params` é lido sem `any`;
- parâmetro opcional possui fallback (`initialParams` ou tratamento local);
- retorno final atualiza contexto na lista de atendimentos.

## 11. Erros comuns

### Quebrar a continuidade do encontro 11

Trocar toda a navegação para stack puro faz perder `tabs + drawer` já implementados.

### Esquecer `headerShown: false` na aba que contém stack

Pode gerar cabeçalho duplicado (`tabs` + `stack`) na mesma tela.

### Usar nomes de campos diferentes entre origem e destino

Exemplo: enviar `clienteNome` e ler `cliente`.

### Ignorar parâmetros opcionais

Sempre trate ausência com `?`, `initialParams` ou fallback local.

## 12. Exercícios de revisão

1. Por que colocar `stack` apenas dentro da aba `Atendimentos` neste cenário?
2. Qual diferença prática entre `navigate` e `push` no fluxo de chamados?
3. Quando faz sentido usar `setParams`?
4. Como `FinalizacaoAtendimento` devolve contexto para `AtendimentosLista`?
5. Qual ganho de manter `tabs + drawer` e adicionar `stack` de forma localizada?

## 13. Exercícios de estudo

- Adicione uma tela `HistoricoAtendimento` no mesmo stack recebendo parâmetros da finalização.
- Faça um filtro por prioridade na lista usando parâmetro de rota.
- Adicione botão no `Dashboard` para abrir diretamente o fluxo de atendimentos.
- Explique em um parágrafo por que `any` é arriscado na tipagem de rotas.

## 14. Resumo do encontro

Neste encontro, você evoluiu o app do encontro 11 sem recomeçar do zero: manteve a navegação estrutural (`tabs + drawer`) e adicionou um fluxo multijanelas tipado na aba de atendimentos. Com isso, o projeto passa a combinar navegação por menu e navegação orientada a dados, usando parâmetros de rota de forma previsível. Essa base prepara o próximo bloco sobre persistência local, onde o contexto do fluxo deixa de ser apenas transitório.

## Materiais complementares

- React Navigation (Passing params): <https://reactnavigation.org/docs/params/>
- React Navigation (TypeScript): <https://reactnavigation.org/docs/typescript>
- React Navigation (Route object): <https://reactnavigation.org/docs/route-object>
- React Navigation (Navigation object): <https://reactnavigation.org/docs/navigation-object>
- React Navigation (Nesting navigators): <https://reactnavigation.org/docs/nesting-navigators>
