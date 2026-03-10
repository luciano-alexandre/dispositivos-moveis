# Encontro 11 - Navegação por abas e drawer

## Objetivos

- Comparar navegação por abas e por menu lateral.
- Escolher um padrão conforme o contexto do app.
- Integrar mais de um navegador na mesma aplicação.

## Conteúdo técnico

Abas funcionam bem para áreas principais e recorrentes do sistema. Drawer funciona melhor quando há muitas seções ou quando as áreas não precisam estar sempre visíveis. O ponto pedagógico aqui é ensinar que navegação é uma decisão de arquitetura de interface.

```tsx
const Tab = createBottomTabNavigator();

function RotasPrincipais() {
  return (
    <Tab.Navigator>
      <Tab.Screen name="Inicio" component={InicioScreen} />
      <Tab.Screen name="Perfil" component={PerfilScreen} />
    </Tab.Navigator>
  );
}
```

## Discussão orientada

- Quando usar abas fixas?
- Quando o drawer melhora a escalabilidade?
- Como evitar que a navegação fique confusa?

## Atividade

- Implementar duas áreas principais com abas.
- Adicionar um drawer com tela de configurações e ajuda.

## Materiais complementares

- Bottom tabs: <https://reactnavigation.org/docs/bottom-tab-navigator>
- Drawer navigator: <https://reactnavigation.org/docs/drawer-navigator>
