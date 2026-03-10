# Encontro 06 - Interfaces em estilo formulário (TextInput, validação)

## Objetivos

- Trabalhar entrada de texto com `TextInput`.
- Introduzir validação de campos.
- Construir feedback visual para erro.

## Desenvolvimento do conteúdo

Formulários são essenciais em apps móveis porque concentram cadastro, login, busca e configuração. O objetivo aqui é aprender não apenas a capturar texto, mas também a validar dados, orientar o usuário e evitar estados inconsistentes.

```tsx
import { useState } from 'react';
import { Button, Text, TextInput, View } from 'react-native';

export default function Formulario() {
  const [email, setEmail] = useState('');
  const emailValido = email.includes('@');

  return (
    <View>
      <TextInput
        value={email}
        onChangeText={setEmail}
        placeholder="Digite seu e-mail"
        keyboardType="email-address"
        autoCapitalize="none"
      />
      {!emailValido && email.length > 0 && <Text>E-mail inválido</Text>}
      <Button title="Enviar" onPress={() => {}} disabled={!emailValido} />
    </View>
  );
}
```

## Boas práticas

- Usar `keyboardType` adequado.
- Configurar `secureTextEntry` para senha.
- Validar cedo, mas sem poluir a experiência.

## Exercícios de estudo

- Criar formulário de cadastro com nome, e-mail e senha.
- Bloquear envio enquanto existir erro.

## Materiais complementares

- TextInput: <https://reactnative.dev/docs/textinput>
- Forms no React: <https://react.dev/reference/react-dom/components/input>
