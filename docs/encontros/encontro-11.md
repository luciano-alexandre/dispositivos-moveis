# Encontro 11 - Navegação por abas e drawer (tab navigation e drawer navigation)

## Visão do encontro

- **Objetivo central:** evoluir a navegação do app para menus persistentes com abas inferiores e menu lateral, organizando melhor o acesso às áreas principais do sistema.
- Ao final deste encontro, você deve ser capaz de testar o app com Expo Go no emulador e no celular, configurar `Bottom Tabs`, montar um `Drawer` e combinar ambos em uma estrutura limpa.

## Roteiro

1. Como usar Expo Go para testar no emulador e no celular.
2. Retomada do encontro 10.
3. Quando usar abas e quando usar drawer.
4. Instalação e preparação do projeto.
5. Estrutura recomendada de arquivos.
6. Montagem do `Bottom Tab Navigator`.
7. Montagem do `Drawer Navigator` e integração com tabs.
8. Exemplo completo com tabs + drawer.
9. Prática 07 guiada.
10. Revisão e exercícios de fixação.

## 1. Como usar Expo Go para testar no emulador e no celular

Antes de avançar na navegação, garanta um fluxo de testes rápido.

Inicie o servidor do projeto:

```bash
npx expo start
```

### Leitura do código

1. `npx expo start`: sobe o servidor de desenvolvimento do Expo.
2. Ao iniciar, ele exibe QR code e atalhos de teclado para abrir o app em diferentes dispositivos.

Com o servidor ativo, você pode abrir o app por emulador/simulador ou por celular físico.

### Android Emulator

1. Abra o Android Studio.
2. Vá em `Device Manager` e inicie um dispositivo virtual (`AVD`).
3. No terminal do Expo, pressione `a`.
4. Aguarde abrir o app no Expo Go do emulador.

Se houver mais de um emulador conectado, use `Shift + a` para escolher o dispositivo.

### iOS Simulator (somente macOS)

1. Inicie o iOS Simulator (via Xcode ou comando `open -a Simulator`).
2. No terminal do Expo, pressione `i`.
3. O projeto será aberto no Expo Go dentro do simulador.

### Celular Android/iOS com Expo Go

1. Instale o app Expo Go no celular.
2. Conecte celular e computador na mesma rede Wi-Fi.
3. Escaneie o QR code mostrado no terminal:
- iOS: use a câmera nativa;
- Android: use o scanner dentro do Expo Go.

Se a rede bloquear conexão local, rode:

```bash
npx expo start --tunnel
```

### Leitura do código

1. `--tunnel`: cria um caminho alternativo de rede quando a conexão local (LAN) falha.
2. É útil em redes públicas, institucionais ou com bloqueios de roteador.

### Atalhos úteis no terminal do Expo

- `a`: abrir no Android;
- `i`: abrir no iOS Simulator;
- `r`: recarregar app;
- `m`: abrir menu de desenvolvedor.

Esse ciclo (editar -> salvar -> validar no Expo Go) deve ser sua rotina padrão durante todo o semestre.

## 2. Retomada do conteúdo anterior

No encontro 10, você implementou fluxo em pilha (`stack`) para navegação de telas sequenciais. Agora vamos complementar essa base com:

- **abas** para acesso rápido a áreas principais;
- **drawer** para navegação lateral e opções secundárias.

Em projetos reais, o padrão mais comum é combinar mais de um tipo de navigator.

## 3. Quando usar abas e quando usar drawer

### `Bottom Tabs`

Use quando:

- o usuário alterna constantemente entre 2 e 5 seções principais;
- é importante manter atalhos sempre visíveis;
- a navegação precisa ser imediata, com poucos toques.

### `Drawer`

Use quando:

- há mais seções do que cabem bem em abas;
- existem telas de configuração, ajuda e áreas menos frequentes;
- você quer liberar espaço visual da tela principal.

### Estratégia prática

- abas: caminhos mais usados no dia a dia;
- drawer: caminhos complementares e administrativos.

## 4. Instalação e preparação do projeto

Partindo de um projeto com `stack` já funcional, instale os pacotes adicionais:

```bash
npx expo install @react-navigation/bottom-tabs
npx expo install @react-navigation/drawer
npx expo install react-native-gesture-handler react-native-reanimated react-native-worklets
```

### Leitura do código

1. `@react-navigation/bottom-tabs`: habilita a navegação por abas inferiores.
2. `@react-navigation/drawer`: habilita o menu lateral.
3. `gesture-handler`, `reanimated` e `worklets`: dependências de gesto/animação usadas pelo drawer.

Se necessário, confirme também as dependências-base:

```bash
npx expo install @react-navigation/native
npx expo install react-native-screens react-native-safe-area-context
```

Observação: se aparecer erro de `reanimated`, revise o `babel.config.js` e reinicie o servidor (`Ctrl + C` e `npx expo start -c`).

## 5. Estrutura recomendada de arquivos

```text
src/
  navigation/
    RootNavigator.tsx
    AppTabs.tsx
    AppDrawer.tsx
  screens/
    DashboardScreen.tsx
    AtendimentosScreen.tsx
    RelatoriosScreen.tsx
    ConfiguracoesScreen.tsx
  styles.ts
App.tsx
```

Separação de responsabilidades:

- `RootNavigator.tsx`: ponto único com `NavigationContainer`;
- `AppTabs.tsx`: abas inferiores;
- `AppDrawer.tsx`: menu lateral e integração com tabs;
- `screens/`: telas reais;
- `styles.ts`: aparência compartilhada.

## 6. Montagem do `Bottom Tab Navigator`

Arquivo `src/navigation/AppTabs.tsx`:

```tsx
import Ionicons from '@expo/vector-icons/Ionicons';
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
import { AtendimentosScreen } from '../screens/AtendimentosScreen';
import { DashboardScreen } from '../screens/DashboardScreen';
import { RelatoriosScreen } from '../screens/RelatoriosScreen';

export type RootTabParamList = {
  Dashboard: undefined;
  Atendimentos: undefined;
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
        component={AtendimentosScreen}
        options={{ title: 'Atendimentos' }}
      />
      <Tab.Screen name="Relatorios" component={RelatoriosScreen} options={{ title: 'Relatórios' }} />
    </Tab.Navigator>
  );
}
```

### Leitura do código

1. `RootTabParamList`: define as rotas válidas e evita erro de nome/parâmetro.
2. `createBottomTabNavigator<...>()`: cria o navigator já tipado com TypeScript.
3. `screenOptions`: centraliza estilos e ícones para todas as abas.
4. `obterIcone(...)`: troca o ícone conforme rota e foco ativo.

## 7. Montagem do `Drawer Navigator` e integração com tabs

Arquivo `src/navigation/AppDrawer.tsx`:

```tsx
import Ionicons from '@expo/vector-icons/Ionicons';
import { createDrawerNavigator } from '@react-navigation/drawer';
import { AppTabs } from './AppTabs';
import { ConfiguracoesScreen } from '../screens/ConfiguracoesScreen';

export type RootDrawerParamList = {
  PainelPrincipal: undefined;
  Configuracoes: undefined;
};

const Drawer = createDrawerNavigator<RootDrawerParamList>();

export function AppDrawer() {
  return (
    <Drawer.Navigator
      initialRouteName="PainelPrincipal"
      screenOptions={({ route }) => ({
        headerStyle: { backgroundColor: '#0f172a' },
        headerTintColor: '#f8fafc',
        drawerActiveTintColor: '#0f766e',
        drawerInactiveTintColor: '#334155',
        drawerIcon: ({ focused, color, size }) => (
          <Ionicons
            name={
              route.name === 'PainelPrincipal'
                ? focused ? 'grid' : 'grid-outline'
                : focused ? 'settings' : 'settings-outline'
            }
            color={color}
            size={size}
          />
        ),
      })}
    >
      <Drawer.Screen
        name="PainelPrincipal"
        component={AppTabs}
        options={{ title: 'Aplicativo', headerShown: false }}
      />
      <Drawer.Screen
        name="Configuracoes"
        component={ConfiguracoesScreen}
        options={{ title: 'Configurações' }}
      />
    </Drawer.Navigator>
  );
}
```

### Leitura do código

1. `RootDrawerParamList`: tipa as entradas do menu lateral.
2. `PainelPrincipal` usa `AppTabs` como conteúdo principal do app.
3. `headerShown: false` evita cabeçalho duplicado quando o drawer envolve tabs.
4. `drawerIcon`: personaliza ícones por item do menu.

Arquivo `src/navigation/RootNavigator.tsx`:

```tsx
import { NavigationContainer } from '@react-navigation/native';
import { AppDrawer } from './AppDrawer';

export function RootNavigator() {
  return (
    <NavigationContainer>
      <AppDrawer />
    </NavigationContainer>
  );
}
```

### Leitura do código

1. `NavigationContainer` é o contêiner raiz do estado de navegação.
2. `AppDrawer` vira o navigator principal e passa a controlar as telas do app.

## 8. Exemplo completo: app com tabs + drawer

Estrutura usada:

```text
src/
  navigation/
    RootNavigator.tsx
    AppTabs.tsx
    AppDrawer.tsx
  screens/
    DashboardScreen.tsx
    AtendimentosScreen.tsx
    RelatoriosScreen.tsx
    ConfiguracoesScreen.tsx
  styles.ts
App.tsx
```

`App.tsx`

```tsx
import { RootNavigator } from './src/navigation/RootNavigator';

export default function App() {
  return <RootNavigator />;
}
```

### Leitura do código

1. `App.tsx` fica mínimo: apenas delega a navegação para o `RootNavigator`.
2. Essa separação facilita manutenção e crescimento da aplicação.

`src/screens/DashboardScreen.tsx`

```tsx
import type { BottomTabScreenProps } from '@react-navigation/bottom-tabs';
import { DrawerActions } from '@react-navigation/native';
import { Pressable, Text, View } from 'react-native';
import type { RootTabParamList } from '../navigation/AppTabs';
import { styles } from '../styles';

type Props = BottomTabScreenProps<RootTabParamList, 'Dashboard'>;

export function DashboardScreen({ navigation }: Props) {
  return (
    <View style={styles.container}>
      <Text style={styles.titulo}>Painel operacional</Text>
      <Text style={styles.subtitulo}>
        Use as abas para alternar as áreas principais e abra o menu lateral para configurações.
      </Text>

      <Pressable
        style={styles.botaoPrimario}
        onPress={() => navigation.navigate('Atendimentos')}
      >
        <Text style={styles.botaoPrimarioTexto}>Ir para Atendimentos</Text>
      </Pressable>

      <Pressable
        style={styles.botaoSecundario}
        onPress={() => navigation.dispatch(DrawerActions.toggleDrawer())}
      >
        <Text style={styles.botaoSecundarioTexto}>Abrir menu lateral</Text>
      </Pressable>
    </View>
  );
}
```

### Leitura do código

1. `BottomTabScreenProps` tipa a navegação da tela de aba.
2. `navigation.navigate('Atendimentos')` muda de aba por código.
3. `DrawerActions.toggleDrawer()` abre/fecha o menu lateral via botão.

`src/screens/AtendimentosScreen.tsx`

```tsx
import type { BottomTabScreenProps } from '@react-navigation/bottom-tabs';
import { Pressable, Text, View } from 'react-native';
import type { RootTabParamList } from '../navigation/AppTabs';
import { styles } from '../styles';

type Props = BottomTabScreenProps<RootTabParamList, 'Atendimentos'>;

export function AtendimentosScreen({ navigation }: Props) {
  return (
    <View style={styles.container}>
      <Text style={styles.titulo}>Atendimentos do dia</Text>
      <Text style={styles.subtitulo}>
        Esta aba representa um fluxo recorrente de trabalho de campo.
      </Text>

      <Pressable style={styles.botaoPrimario} onPress={() => navigation.navigate('Relatorios')}>
        <Text style={styles.botaoPrimarioTexto}>Ver Relatórios</Text>
      </Pressable>
    </View>
  );
}
```

`src/screens/RelatoriosScreen.tsx`

```tsx
import { Text, View } from 'react-native';
import { styles } from '../styles';

export function RelatoriosScreen() {
  return (
    <View style={styles.container}>
      <Text style={styles.titulo}>Relatórios</Text>
      <Text style={styles.subtitulo}>
        Nesta área você pode consolidar números e evolução semanal.
      </Text>
    </View>
  );
}
```

`src/screens/ConfiguracoesScreen.tsx`

```tsx
import { Text, View } from 'react-native';
import { styles } from '../styles';

export function ConfiguracoesScreen() {
  return (
    <View style={styles.container}>
      <Text style={styles.titulo}>Configurações</Text>
      <Text style={styles.subtitulo}>
        Tela acessada pelo drawer para opções menos frequentes do app.
      </Text>
    </View>
  );
}
```

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
    marginBottom: 8,
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
});
```

### Leitura do código

1. `StyleSheet.create` centraliza estilos reutilizáveis em todas as telas.
2. O padrão `botaoPrimario`/`botaoSecundario` mantém consistência visual entre ações.

Com esse desenho, você obtém:

- acesso rápido por abas;
- menu lateral para áreas complementares;
- estrutura escalável para novos módulos.

## 9. Prática 07

### Objetivo

Construir o app **Central de Campo** com abas e drawer integrados.

### Requisitos mínimos

1. Criar três abas: `Inicio`, `Tarefas` e `Resumo`.
2. Criar `Drawer` com duas entradas: `Aplicativo` (tabs) e `Configuracoes`.
3. Usar ícones nas abas e no drawer.
4. Definir cor ativa e inativa dos itens de navegação.
5. Adicionar ao menos um botão em uma aba para abrir o drawer.
6. Validar o comportamento no emulador e no celular com Expo Go.
7. Organizar arquivos em `navigation/`, `screens/` e `styles.ts`.

### Entrega esperada

- navegação sem travamentos;
- alternância fluida entre abas;
- drawer abrindo por gesto e botão;
- código com tipagem de rotas.

## 10. Checklist de validação do aluno

- servidor Expo inicia com `npx expo start`;
- app abre no emulador (`a`/`i`) e no celular via QR code;
- tabs exibem ícones e labels corretos;
- drawer possui opções corretas e abre/fecha normalmente;
- tela de configuração é acessível pelo menu lateral;
- estrutura de arquivos está separada por responsabilidade.

## 11. Erros comuns

### Esquecer dependências de gesto e animação do drawer

Sem elas, o menu lateral pode quebrar na execução.

### Colocar `NavigationContainer` em mais de um lugar

Deixe apenas no `RootNavigator` para evitar estado duplicado de navegação.

### Misturar tudo no `App.tsx`

A navegação cresce rápido. Separe navegadores e telas desde o início.

### Não testar no dispositivo real

Somente emulador não cobre diferenças de rede, densidade e comportamento de toque.

## 12. Exercícios de revisão

1. Qual cenário favorece `Bottom Tabs` em vez de `Drawer`?
2. Qual a principal função do `RootNavigator`?
3. Quando usar `DrawerActions.toggleDrawer()`?
4. Por que separar `AppTabs` e `AppDrawer` em arquivos diferentes?
5. Em caso de problema de conexão no celular, quando usar `--tunnel`?

## 13. Exercícios de estudo

- Adicione uma quarta aba chamada `Alertas`.
- Crie um badge simples na aba `Tarefas` para indicar pendências.
- Personalize o conteúdo do drawer com cabeçalho e rodapé.
- Explique em um parágrafo a diferença entre navegação sequencial (`stack`) e navegação por menu (`tabs`/`drawer`).

## 14. Resumo do encontro

Neste encontro, você avançou para um modelo de navegação mais próximo de apps reais: menu principal por abas e opções complementares em drawer. Também consolidou o fluxo de testes com Expo Go no emulador e no celular, o que reduz atrito no desenvolvimento diário. Com essa base, o próximo passo é trocar dados entre telas com parâmetros tipados, criando fluxos multijanelas mais inteligentes.

## Materiais complementares

- Expo docs (Start developing): <https://docs.expo.dev/get-started/start-developing/>
- Expo docs (Android Studio Emulator): <https://docs.expo.dev/workflow/android-studio-emulator/>
- React Navigation (Bottom Tabs): <https://reactnavigation.org/docs/bottom-tab-navigator>
- React Navigation (Drawer Navigator): <https://reactnavigation.org/docs/drawer-navigator/>
- React Navigation (Nesting navigators): <https://reactnavigation.org/docs/nesting-navigators>
