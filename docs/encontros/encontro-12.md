# Encontro 12 - Parâmetros entre telas e fluxo multijanelas

## Visão do encontro

- **Objetivo central:** implementar troca de dados entre telas com parâmetros tipados no React Navigation, mantendo previsibilidade no fluxo e melhorando a comunicação entre etapas do app.
- Ao final deste encontro, você deve ser capaz de enviar e ler parâmetros com segurança, definir parâmetros opcionais, atualizar `route.params` e organizar um fluxo multijanelas coerente.

## Roteiro

1. Retomada dos encontros 10 e 11.
2. O que são parâmetros de rota e por que usar.
3. Tipagem de parâmetros com TypeScript.
4. Enviando parâmetros com `navigate` e `push`.
5. Lendo parâmetros com `route.params`.
6. Parâmetros opcionais, `initialParams` e `setParams`.
7. Exemplo completo de fluxo multijanelas com parâmetros.
8. Estratégias para retorno de dados no fluxo.
9. Exercício de rotas guiado.
10. Revisão e exercícios de fixação.

## 1. Retomada dos encontros 10 e 11

Progressão até aqui:

- encontro 10: navegação por pilha (`stack`) para fluxo sequencial;
- encontro 11: menus por abas e drawer para acesso estrutural.

Agora vamos adicionar contexto às telas: além de navegar, cada etapa passa dados para a próxima.

## 2. O que são parâmetros de rota e por que usar

Parâmetros de rota são dados enviados no momento da navegação, por exemplo:

```tsx
navigation.navigate('DetalhesPedido', { pedidoId: 'PED-103', cliente: 'Ana' });
```

### Leitura do código

1. O primeiro argumento (`'DetalhesPedido'`) é o nome exato da rota de destino.
2. O segundo argumento é um objeto com os parâmetros que a tela de destino irá consumir.

Uso recomendado:

- identificar o que a próxima tela deve mostrar (ex.: `id`, `filtro`, `modo`);
- carregar dados específicos com base nesses identificadores;
- manter fluxo explícito e legível.

Boa prática:

- prefira dados pequenos e serializáveis em JSON;
- passe identificadores em vez de objetos gigantes;
- mantenha consistência na tipagem entre origem e destino.

## 3. Tipagem de parâmetros com TypeScript

Crie a lista de rotas com seus parâmetros:

```tsx
export type RootStackParamList = {
  ListaPedidos: { ultimoPedidoId?: string; ultimaAcao?: 'concluido' | 'pendente' } | undefined;
  DetalhesPedido: {
    pedidoId: string;
    cliente: string;
    prioridade?: 'alta' | 'normal';
  };
  Finalizacao: {
    pedidoId: string;
    tecnico: string;
    status: 'concluido' | 'pendente';
    observacao?: string;
  };
};
```

### Leitura do código

1. `ListaPedidos`: rota inicial, podendo receber retorno opcional.
2. `DetalhesPedido`: exige `pedidoId` e `cliente`.
3. `Finalizacao`: exige dados de fechamento da operação.
4. Campos opcionais (`?`) permitem flexibilidade sem quebrar contrato.

Com isso, o TypeScript acusa erro se você tentar navegar sem os parâmetros obrigatórios.

## 4. Enviando parâmetros com `navigate` e `push`

Exemplos práticos:

```tsx
navigation.navigate('DetalhesPedido', {
  pedidoId: 'PED-204',
  cliente: 'Carlos Menezes',
  prioridade: 'alta',
});

navigation.push('DetalhesPedido', {
  pedidoId: 'PED-205',
  cliente: 'Marina F.',
});
```

### Leitura do código

1. `navigate` reutiliza a rota quando possível e direciona para ela.
2. `push` sempre cria uma nova instância da mesma tela na pilha.

Diferença prática:

- `navigate`: tenta ir para a rota já existente no fluxo;
- `push`: sempre empilha nova instância da mesma tela.

## 5. Lendo parâmetros com `route.params`

Dentro da tela de destino:

```tsx
import type { NativeStackScreenProps } from '@react-navigation/native-stack';
import type { RootStackParamList } from '../navigation/AppStack';

type Props = NativeStackScreenProps<RootStackParamList, 'DetalhesPedido'>;

export function DetalhesPedidoScreen({ route }: Props) {
  const { pedidoId, cliente, prioridade } = route.params;

  return (
    <>
      {/* renderização */}
    </>
  );
}
```

### Leitura do código

1. `NativeStackScreenProps` injeta tipagem de `navigation` e `route`.
2. `route.params` passa a ter contrato explícito, sem necessidade de `any`.

Como as props já estão tipadas, o editor oferece autocompletar e alerta caso você use um campo inexistente.

## 6. Parâmetros opcionais, `initialParams` e `setParams`

Você pode definir parâmetros padrão no `Screen`:

```tsx
<Stack.Screen
  name="DetalhesPedido"
  component={DetalhesPedidoScreen}
  initialParams={{ prioridade: 'normal' }}
/>
```

Também é possível atualizar parâmetros da tela atual:

```tsx
navigation.setParams({ prioridade: 'alta' });
```

### Leitura do código

1. `initialParams` define valor padrão quando a rota abre sem parâmetros.
2. `setParams` atualiza os parâmetros da rota atual sem recriar a tela.

Pontos de atenção:

- `setParams` faz mesclagem (`merge`) com parâmetros atuais;
- use parâmetros para estado de rota, não para dados pesados da aplicação;
- evite nomes reservados como `screen`, `params`, `initial` e `state`.

## 7. Exemplo completo: fluxo multijanelas com parâmetros

Estrutura usada:

```text
src/
  navigation/
    AppStack.tsx
  screens/
    ListaPedidosScreen.tsx
    DetalhesPedidoScreen.tsx
    FinalizacaoScreen.tsx
  styles.ts
App.tsx
```

`App.tsx`

```tsx
import { AppStack } from './src/navigation/AppStack';

export default function App() {
  return <AppStack />;
}
```

### Leitura do código

1. `App.tsx` delega toda a navegação para um único componente de stack.
2. Isso evita acoplamento da regra de rotas com a tela inicial do projeto.

`src/navigation/AppStack.tsx`

```tsx
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import { DetalhesPedidoScreen } from '../screens/DetalhesPedidoScreen';
import { FinalizacaoScreen } from '../screens/FinalizacaoScreen';
import { ListaPedidosScreen } from '../screens/ListaPedidosScreen';

export type RootStackParamList = {
  ListaPedidos: { ultimoPedidoId?: string; ultimaAcao?: 'concluido' | 'pendente' } | undefined;
  DetalhesPedido: {
    pedidoId: string;
    cliente: string;
    prioridade?: 'alta' | 'normal';
  };
  Finalizacao: {
    pedidoId: string;
    tecnico: string;
    status: 'concluido' | 'pendente';
    observacao?: string;
  };
};

const Stack = createNativeStackNavigator<RootStackParamList>();

export function AppStack() {
  return (
    <NavigationContainer>
      <Stack.Navigator
        initialRouteName="ListaPedidos"
        screenOptions={{
          headerStyle: { backgroundColor: '#0f172a' },
          headerTintColor: '#f8fafc',
          contentStyle: { backgroundColor: '#f8fafc' },
        }}
      >
        <Stack.Screen name="ListaPedidos" component={ListaPedidosScreen} options={{ title: 'Pedidos' }} />
        <Stack.Screen
          name="DetalhesPedido"
          component={DetalhesPedidoScreen}
          options={{ title: 'Detalhes do Pedido' }}
          initialParams={{ prioridade: 'normal' }}
        />
        <Stack.Screen
          name="Finalizacao"
          component={FinalizacaoScreen}
          options={{ title: 'Finalização' }}
        />
      </Stack.Navigator>
    </NavigationContainer>
  );
}
```

### Leitura do código

1. `RootStackParamList` concentra o contrato de dados entre telas.
2. `createNativeStackNavigator<...>()` aplica esse contrato em toda a pilha.
3. `initialParams` em `DetalhesPedido` garante prioridade padrão (`normal`).

`src/screens/ListaPedidosScreen.tsx`

```tsx
import type { NativeStackScreenProps } from '@react-navigation/native-stack';
import { Pressable, Text, View } from 'react-native';
import type { RootStackParamList } from '../navigation/AppStack';
import { styles } from '../styles';

type Props = NativeStackScreenProps<RootStackParamList, 'ListaPedidos'>;

const pedidos = [
  { id: 'PED-101', cliente: 'Ana Souza', prioridade: 'alta' as const },
  { id: 'PED-102', cliente: 'Bruno Lima', prioridade: 'normal' as const },
  { id: 'PED-103', cliente: 'Cecilia Paiva', prioridade: 'normal' as const },
];

export function ListaPedidosScreen({ navigation, route }: Props) {
  const ultimoPedido = route.params?.ultimoPedidoId;
  const ultimaAcao = route.params?.ultimaAcao;

  return (
    <View style={styles.container}>
      <Text style={styles.titulo}>Fila de pedidos</Text>
      <Text style={styles.subtitulo}>
        Toque em um item para abrir detalhes com parâmetros da rota.
      </Text>

      {ultimoPedido ? (
        <View style={styles.alerta}>
          <Text style={styles.alertaTexto}>
            Último retorno: {ultimoPedido} ({ultimaAcao})
          </Text>
        </View>
      ) : null}

      {pedidos.map((pedido) => (
        <Pressable
          key={pedido.id}
          style={styles.item}
          onPress={() =>
            navigation.navigate('DetalhesPedido', {
              pedidoId: pedido.id,
              cliente: pedido.cliente,
              prioridade: pedido.prioridade,
            })
          }
        >
          <Text style={styles.itemTitulo}>{pedido.id}</Text>
          <Text style={styles.itemSubtitulo}>{pedido.cliente}</Text>
        </Pressable>
      ))}
    </View>
  );
}
```

### Leitura do código

1. `route.params?.ultimoPedidoId` lê retorno opcional sem quebrar renderização.
2. `pedidos.map(...)` cria a lista de botões de navegação dinamicamente.
3. Cada `navigate('DetalhesPedido', ...)` envia contexto do item selecionado.

`src/screens/DetalhesPedidoScreen.tsx`

```tsx
import { useState } from 'react';
import type { NativeStackScreenProps } from '@react-navigation/native-stack';
import { Alert, Pressable, Text, TextInput, View } from 'react-native';
import type { RootStackParamList } from '../navigation/AppStack';
import { styles } from '../styles';

type Props = NativeStackScreenProps<RootStackParamList, 'DetalhesPedido'>;

export function DetalhesPedidoScreen({ navigation, route }: Props) {
  const { pedidoId, cliente, prioridade = 'normal' } = route.params;
  const [tecnico, setTecnico] = useState('');
  const [status, setStatus] = useState<'concluido' | 'pendente'>('concluido');

  function avancar() {
    if (!tecnico.trim()) {
      Alert.alert('Atenção', 'Informe o nome do técnico para continuar.');
      return;
    }

    navigation.navigate('Finalizacao', {
      pedidoId,
      tecnico: tecnico.trim(),
      status,
      observacao: prioridade === 'alta' ? 'Prioridade alta atendida.' : undefined,
    });
  }

  return (
    <View style={styles.container}>
      <Text style={styles.titulo}>Detalhes do atendimento</Text>
      <Text style={styles.subtitulo}>Pedido: {pedidoId}</Text>
      <Text style={styles.subtitulo}>Cliente: {cliente}</Text>
      <Text style={styles.subtitulo}>Prioridade: {prioridade}</Text>

      <TextInput
        style={styles.input}
        placeholder="Técnico responsável"
        value={tecnico}
        onChangeText={setTecnico}
      />

      <View style={styles.linhaBotoes}>
        <Pressable style={styles.botaoSecundario} onPress={() => setStatus('concluido')}>
          <Text style={styles.botaoSecundarioTexto}>Concluído</Text>
        </Pressable>
        <Pressable style={styles.botaoSecundario} onPress={() => setStatus('pendente')}>
          <Text style={styles.botaoSecundarioTexto}>Pendente</Text>
        </Pressable>
      </View>

      <Pressable style={styles.botaoPrimario} onPress={avancar}>
        <Text style={styles.botaoPrimarioTexto}>Avançar para finalização</Text>
      </Pressable>
    </View>
  );
}
```

### Leitura do código

1. `useState` controla técnico e status antes de avançar no fluxo.
2. A função `avancar()` valida os campos e bloqueia envio inválido.
3. `navigation.navigate('Finalizacao', ...)` encaminha os dados consolidados.

`src/screens/FinalizacaoScreen.tsx`

```tsx
import type { NativeStackScreenProps } from '@react-navigation/native-stack';
import { Pressable, Text, View } from 'react-native';
import type { RootStackParamList } from '../navigation/AppStack';
import { styles } from '../styles';

type Props = NativeStackScreenProps<RootStackParamList, 'Finalizacao'>;

export function FinalizacaoScreen({ navigation, route }: Props) {
  const { pedidoId, tecnico, status, observacao } = route.params;

  return (
    <View style={styles.container}>
      <Text style={styles.titulo}>Resumo final</Text>
      <Text style={styles.subtitulo}>Pedido: {pedidoId}</Text>
      <Text style={styles.subtitulo}>Técnico: {tecnico}</Text>
      <Text style={styles.subtitulo}>Status: {status}</Text>
      {observacao ? <Text style={styles.subtitulo}>{observacao}</Text> : null}

      <Pressable
        style={styles.botaoPrimario}
        onPress={() =>
          navigation.navigate('ListaPedidos', {
            ultimoPedidoId: pedidoId,
            ultimaAcao: status,
          })
        }
      >
        <Text style={styles.botaoPrimarioTexto}>Concluir e voltar para lista</Text>
      </Pressable>
    </View>
  );
}
```

### Leitura do código

1. A tela lê os parâmetros finais para montar o resumo.
2. O botão retorna para `ListaPedidos` já enviando dados da última operação.

`src/styles.ts`

```tsx
import { StyleSheet } from 'react-native';

export const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#f8fafc',
    padding: 16,
    justifyContent: 'center',
    gap: 10,
  },
  titulo: {
    fontSize: 22,
    fontWeight: '700',
    color: '#0f172a',
  },
  subtitulo: {
    fontSize: 14,
    lineHeight: 20,
    color: '#475569',
  },
  alerta: {
    backgroundColor: '#dcfce7',
    borderRadius: 10,
    padding: 10,
    borderWidth: 1,
    borderColor: '#16a34a',
  },
  alertaTexto: {
    color: '#166534',
    fontWeight: '600',
  },
  item: {
    backgroundColor: '#ffffff',
    borderRadius: 10,
    borderWidth: 1,
    borderColor: '#cbd5e1',
    padding: 12,
    gap: 2,
  },
  itemTitulo: {
    color: '#0f172a',
    fontWeight: '700',
  },
  itemSubtitulo: {
    color: '#475569',
  },
  input: {
    borderWidth: 1,
    borderColor: '#cbd5e1',
    borderRadius: 10,
    backgroundColor: '#ffffff',
    paddingHorizontal: 12,
    paddingVertical: 10,
  },
  linhaBotoes: {
    flexDirection: 'row',
    gap: 10,
  },
  botaoPrimario: {
    marginTop: 8,
    backgroundColor: '#0f766e',
    borderRadius: 10,
    paddingVertical: 12,
    alignItems: 'center',
  },
  botaoPrimarioTexto: {
    color: '#ffffff',
    fontWeight: '700',
  },
  botaoSecundario: {
    flex: 1,
    borderWidth: 1,
    borderColor: '#0f766e',
    borderRadius: 10,
    paddingVertical: 12,
    alignItems: 'center',
  },
  botaoSecundarioTexto: {
    color: '#0f766e',
    fontWeight: '700',
  },
});
```

### Leitura do código

1. Estilos de lista, alerta e botões ficam centralizados e reutilizáveis.
2. A separação visual reduz repetição e deixa as telas mais legíveis.

Com esse fluxo, cada tela recebe apenas o contexto necessário para continuar o processo.

## 8. Estratégias para retorno de dados no fluxo

Há duas estratégias simples no início:

1. voltar com `goBack()` quando não precisa enviar nada;
2. voltar navegando para a tela anterior com parâmetros (como no exemplo de `Finalizacao` -> `ListaPedidos`).

Isso evita estado global desnecessário quando o dado é local ao fluxo.

## 9. Exercício de rotas

### Objetivo

Montar o app **Fluxo de Chamados** com três telas e passagem de parâmetros tipados.

### Requisitos mínimos

1. Tela `Chamados` com lista de ao menos 4 itens.
2. Tela `DetalhesChamado` recebendo `id`, `solicitante` e `prioridade`.
3. Tela `Encerramento` recebendo dados da etapa anterior.
4. Uso de `navigate` com parâmetros obrigatórios corretamente tipados.
5. Pelo menos um campo opcional nos parâmetros de rota.
6. Retorno para tela inicial com resumo da última operação.
7. Validação de ao menos um campo antes do avanço para a tela final.

### Entrega esperada

- parâmetros chegam corretamente em todas as telas;
- tipagem acusa erros ao remover parâmetros obrigatórios;
- fluxo completo sem navegação quebrada;
- código organizado por `navigation/`, `screens/` e `styles.ts`.

## 10. Checklist de validação do aluno

- `RootStackParamList` está definido e coerente;
- todas as telas usam `NativeStackScreenProps` com tipo correto;
- `navigate` envia os parâmetros esperados;
- `route.params` é lido sem usar `any`;
- parâmetros opcionais são tratados com fallback quando necessário;
- retorno final atualiza contexto da tela inicial.

## 11. Erros comuns

### Passar objeto grande inteiro em vez de identificador

Isso aumenta acoplamento e dificulta manutenção.

### Usar nome de campo diferente entre origem e destino

Exemplo: enviar `clienteNome` e tentar ler `cliente`.

### Ignorar possibilidade de parâmetro opcional ausente

Sempre trate com `?`, fallback ou `initialParams`.

### Misturar `navigate` e `push` sem entender o efeito na pilha

Pode gerar duplicação de tela ou comportamento inesperado de volta.

## 12. Exercícios de revisão

1. Qual diferença prática entre `navigate` e `push` ao enviar parâmetros?
2. Quando usar `initialParams`?
3. O que `setParams` faz com os parâmetros existentes?
4. Por que preferir `pedidoId` em vez de enviar todo o objeto do pedido?
5. Como tratar parâmetros opcionais com segurança?

## 13. Exercícios de estudo

- Adicione filtro por status na tela inicial usando parâmetro de rota.
- Crie um botão para atualizar prioridade da rota atual via `setParams`.
- Implemente uma quarta tela de histórico recebendo parâmetros do encerramento.
- Escreva um parágrafo explicando riscos de usar `any` na tipagem de rotas.

## 14. Resumo do encontro

Neste encontro, você passou de uma navegação apenas estrutural para uma navegação orientada a dados, usando parâmetros tipados entre telas. A prática mostrou como enviar, ler e atualizar `route.params` de forma previsível, além de fechar ciclos multijanelas com retorno de contexto para a tela inicial. Essa base prepara o próximo bloco de conteúdos sobre persistência local, onde os dados deixam de ser apenas transitórios no fluxo de navegação.

## Materiais complementares

- React Navigation (Passing params): <https://reactnavigation.org/docs/params/>
- React Navigation (TypeScript): <https://reactnavigation.org/docs/typescript>
- React Navigation (Route object): <https://reactnavigation.org/docs/route-object>
- React Navigation (Navigation object): <https://reactnavigation.org/docs/navigation-object>
- React Navigation (Nesting navigators): <https://reactnavigation.org/docs/nesting-navigators>
