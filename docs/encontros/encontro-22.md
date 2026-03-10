# Encontro 22 - Autenticação básica e armazenamento de token

## Objetivos

- Compreender fluxo de login.
- Armazenar token com responsabilidade.
- Proteger rotas autenticadas.

## Explicação técnica

Autenticação em apps móveis geralmente envolve envio de credenciais, recebimento de token e uso desse token em chamadas subsequentes. O ponto pedagógico importante é diferenciar autenticação de autorização e mostrar que armazenamento de credenciais exige mais cuidado do que preferências comuns.

```tsx
const resposta = await fetch('/login', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ email, senha }),
});
```

```tsx
const headers = { Authorization: `Bearer ${token}` };
```

## Debate técnico

- Token em AsyncStorage vs armazenamento seguro.
- Expiração de sessão.
- Logout e limpeza de dados.

## Atividade

- Simular login com API de teste.
- Guardar token e redirecionar para área autenticada.

## Materiais complementares

- Expo SecureStore: <https://docs.expo.dev/versions/latest/sdk/securestore/>
- Authentication flows: <https://reactnavigation.org/docs/auth-flow>
