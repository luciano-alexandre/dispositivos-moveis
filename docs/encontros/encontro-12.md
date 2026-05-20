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
7. Exemplo completo (continuação do app do encontro 11).
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

## 7. Exemplo completo: continuação do app do encontro 11

Estrutura usada:

```text
src/
  navigation/
    RootNavigator.tsx
    AppDrawer.tsx
    AppTabs.tsx
    AtendimentosStack.tsx
  screens/
    DashboardScreen.tsx
    AtendimentosListaScreen.tsx
    DetalhesAtendimentoScreen.tsx
    FinalizacaoAtendimentoScreen.tsx
    RelatoriosScreen.tsx
    ConfiguracoesScreen.tsx
  styles.ts
App.tsx
```

`RootNavigator.tsx`, `AppDrawer.tsx`, `DashboardScreen.tsx`, `RelatoriosScreen.tsx` e `ConfiguracoesScreen.tsx` permanecem como no encontro 11. A evolução acontece na aba de atendimentos.

`App.tsx`

```tsx
import { RootNavigator } from './src/navigation/RootNavigator';

export default function App() {
  return <RootNavigator />;
}
```

`src/navigation/AppTabs.tsx`

```tsx
import Ionicons from '@expo/vector-icons/Ionicons';
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
import type { NavigatorScreenParams } from '@react-navigation/native';
import type { AtendimentosStackParamList } from './AtendimentosStack';
import { AtendimentosStack } from './AtendimentosStack';
import { DashboardScreen } from '../screens/DashboardScreen';
import { RelatoriosScreen } from '../screens/RelatoriosScreen';

export type RootTabParamList = {
  Dashboard: undefined;
  Atendimentos: NavigatorScreenParams<AtendimentosStackParamList> | undefined;
  Relatorios: undefined;
};

const Tab = createBottomTabNavigator<RootTabParamList>();

function obterIcone(nomeRota: keyof RootTabParamList, focused: boolean) {
  if (nomeRota === 'Dashboard') return focused ? 'home' : 'home-outline';
  if (nomeRota === 'Atendimentos') return focused ? 'list' : 'list-outline';
  return focused ? 'bar-chart' : 'bar-chart-outline';
}

export function AppTabs() {
  return (
    <Tab.Navigator
      initialRouteName="Dashboard"
      screenOptions={({ route }) => ({
        headerStyle: { backgroundColor: '#0f172a' },
        headerTintColor: '#f8fafc',
        tabBarActiveTintColor: '#0f766e',
        tabBarInactiveTintColor: '#64748b',
        tabBarStyle: { height: 62, paddingBottom: 8, paddingTop: 6 },
        tabBarIcon: ({ focused, color, size }) => (
          <Ionicons
            name={obterIcone(route.name as keyof RootTabParamList, focused)}
            color={color}
            size={size}
          />
        ),
      })}
    >
      <Tab.Screen name="Dashboard" component={DashboardScreen} options={{ title: 'Painel' }} />
      <Tab.Screen
        name="Atendimentos"
        component={AtendimentosStack}
        options={{ title: 'Atendimentos', headerShown: false }}
      />
      <Tab.Screen name="Relatorios" component={RelatoriosScreen} options={{ title: 'Relatórios' }} />
    </Tab.Navigator>
  );
}
```

### Leitura do código

1. A aba `Atendimentos` agora aponta para um `stack` interno.
2. O app mantém `tabs + drawer`, mas ganha fluxo sequencial dentro da aba.

`src/navigation/AtendimentosStack.tsx`

```tsx
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import { AtendimentosListaScreen } from '../screens/AtendimentosListaScreen';
import { DetalhesAtendimentoScreen } from '../screens/DetalhesAtendimentoScreen';
import { FinalizacaoAtendimentoScreen } from '../screens/FinalizacaoAtendimentoScreen';

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

const Stack = createNativeStackNavigator<AtendimentosStackParamList>();

export function AtendimentosStack() {
  return (
    <Stack.Navigator
      initialRouteName="AtendimentosLista"
      screenOptions={{
        headerStyle: { backgroundColor: '#0f172a' },
        headerTintColor: '#f8fafc',
        contentStyle: { backgroundColor: '#f8fafc' },
      }}
    >
      <Stack.Screen
        name="AtendimentosLista"
        component={AtendimentosListaScreen}
        options={{ title: 'Atendimentos do dia' }}
      />
      <Stack.Screen
        name="DetalhesAtendimento"
        component={DetalhesAtendimentoScreen}
        options={{ title: 'Detalhes do Atendimento' }}
        initialParams={{ prioridade: 'normal' }}
      />
      <Stack.Screen
        name="FinalizacaoAtendimento"
        component={FinalizacaoAtendimentoScreen}
        options={{ title: 'Finalização' }}
      />
    </Stack.Navigator>
  );
}
```

### Leitura do código

1. `AtendimentosStackParamList` concentra o contrato de parâmetros.
2. `initialParams` evita tela sem valor de prioridade.

`src/screens/AtendimentosListaScreen.tsx`

```tsx
import type { NativeStackScreenProps } from '@react-navigation/native-stack';
import { Pressable, Text, View } from 'react-native';
import type { AtendimentosStackParamList } from '../navigation/AtendimentosStack';
import { styles } from '../styles';

type Props = NativeStackScreenProps<AtendimentosStackParamList, 'AtendimentosLista'>;

const chamados = [
  { id: 'CH-101', cliente: 'Ana Souza', prioridade: 'alta' as const },
  { id: 'CH-102', cliente: 'Bruno Lima', prioridade: 'normal' as const },
  { id: 'CH-103', cliente: 'Cecilia Paiva', prioridade: 'normal' as const },
];

export function AtendimentosListaScreen({ navigation, route }: Props) {
  const ultimoChamado = route.params?.ultimoChamadoId;
  const ultimaAcao = route.params?.ultimaAcao;

  return (
    <View style={styles.container}>
      <Text style={styles.titulo}>Fila de atendimentos</Text>
      <Text style={styles.subtitulo}>Toque em um chamado para abrir os detalhes com parâmetros.</Text>

      {ultimoChamado ? (
        <View style={styles.alerta}>
          <Text style={styles.alertaTexto}>
            Último retorno: {ultimoChamado} ({ultimaAcao})
          </Text>
        </View>
      ) : null}

      {chamados.map((chamado) => (
        <Pressable
          key={chamado.id}
          style={styles.item}
          onPress={() =>
            navigation.navigate('DetalhesAtendimento', {
              chamadoId: chamado.id,
              cliente: chamado.cliente,
              prioridade: chamado.prioridade,
            })
          }
        >
          <Text style={styles.itemTitulo}>{chamado.id}</Text>
          <Text style={styles.itemSubtitulo}>{chamado.cliente}</Text>
        </Pressable>
      ))}
    </View>
  );
}
```

### Leitura do código

1. `route.params?.ultimoChamadoId` recebe retorno opcional da finalização.
2. Cada item usa `navigate` enviando contexto do chamado.

`src/screens/DetalhesAtendimentoScreen.tsx`

```tsx
import { useState } from 'react';
import type { NativeStackScreenProps } from '@react-navigation/native-stack';
import { Alert, Pressable, Text, TextInput, View } from 'react-native';
import type { AtendimentosStackParamList } from '../navigation/AtendimentosStack';
import { styles } from '../styles';

type Props = NativeStackScreenProps<AtendimentosStackParamList, 'DetalhesAtendimento'>;

export function DetalhesAtendimentoScreen({ navigation, route }: Props) {
  const { chamadoId, cliente, prioridade = 'normal' } = route.params;
  const [tecnico, setTecnico] = useState('');
  const [status, setStatus] = useState<'concluido' | 'pendente'>('concluido');

  function avancar() {
    if (!tecnico.trim()) {
      Alert.alert('Atenção', 'Informe o nome do técnico para continuar.');
      return;
    }

    navigation.navigate('FinalizacaoAtendimento', {
      chamadoId,
      tecnico: tecnico.trim(),
      status,
      observacao: prioridade === 'alta' ? 'Chamado de prioridade alta tratado.' : undefined,
    });
  }

  return (
    <View style={styles.container}>
      <Text style={styles.titulo}>Detalhes do atendimento</Text>
      <Text style={styles.subtitulo}>Chamado: {chamadoId}</Text>
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

      <Pressable
        style={styles.botaoSecundario}
        onPress={() => navigation.setParams({ prioridade: 'alta' })}
      >
        <Text style={styles.botaoSecundarioTexto}>Marcar prioridade alta (setParams)</Text>
      </Pressable>

      <Pressable style={styles.botaoPrimario} onPress={avancar}>
        <Text style={styles.botaoPrimarioTexto}>Avançar para finalização</Text>
      </Pressable>

      <Pressable
        style={styles.botaoTexto}
        onPress={() =>
          navigation.push('DetalhesAtendimento', {
            chamadoId: 'CH-999',
            cliente: 'Novo chamado em campo',
          })
        }
      >
        <Text style={styles.botaoTextoLabel}>Simular próximo chamado (push)</Text>
      </Pressable>
    </View>
  );
}
```

### Leitura do código

1. `route.params` chega tipado com os dados do chamado.
2. `setParams` altera a prioridade da rota atual sem remontar a tela.
3. `push` empilha uma nova instância de detalhes para demonstrar fluxo repetido.

`src/screens/FinalizacaoAtendimentoScreen.tsx`

```tsx
import type { NativeStackScreenProps } from '@react-navigation/native-stack';
import { Pressable, Text, View } from 'react-native';
import type { AtendimentosStackParamList } from '../navigation/AtendimentosStack';
import { styles } from '../styles';

type Props = NativeStackScreenProps<AtendimentosStackParamList, 'FinalizacaoAtendimento'>;

export function FinalizacaoAtendimentoScreen({ navigation, route }: Props) {
  const { chamadoId, tecnico, status, observacao } = route.params;

  return (
    <View style={styles.container}>
      <Text style={styles.titulo}>Resumo final</Text>
      <Text style={styles.subtitulo}>Chamado: {chamadoId}</Text>
      <Text style={styles.subtitulo}>Técnico: {tecnico}</Text>
      <Text style={styles.subtitulo}>Status: {status}</Text>
      {observacao ? <Text style={styles.subtitulo}>{observacao}</Text> : null}

      <Pressable
        style={styles.botaoPrimario}
        onPress={() =>
          navigation.navigate('AtendimentosLista', {
            ultimoChamadoId: chamadoId,
            ultimaAcao: status,
          })
        }
      >
        <Text style={styles.botaoPrimarioTexto}>Concluir e voltar para a lista</Text>
      </Pressable>
    </View>
  );
}
```

### Leitura do código

1. A tela final recebe os parâmetros consolidados.
2. O botão retorna para a lista com contexto da última operação.

`src/styles.ts` (adições para o fluxo de atendimentos)

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
  botaoTexto: {
    marginTop: 6,
    alignItems: 'center',
  },
  botaoTextoLabel: {
    color: '#0f766e',
    textDecorationLine: 'underline',
    fontWeight: '600',
  },
});
```

### Leitura do código

1. O padrão visual do encontro 11 foi mantido.
2. Apenas foram adicionados estilos para lista, alerta e ações do fluxo multijanelas.

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
