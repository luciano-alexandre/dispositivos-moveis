# Encontro 08 - Correção passo a passo da atividade avaliativa 01

## Visão do encontro

- **Objetivo central:** reconstruir uma solução completa e funcional para a atividade avaliativa do app **Vistoria Rápida**, mostrando a sequência de implementação e a responsabilidade de cada arquivo.
- Ao final deste encontro, você deve ser capaz de montar uma tela única em React Native com componentes reutilizáveis, formulário controlado, validação condicional, resumo em tempo real e ações de registro e limpeza.

## Roteiro

1. Relembrar o problema e os requisitos.
2. Criar a estrutura de arquivos.
3. Definir tipos compartilhados.
4. Construir os componentes reutilizáveis.
5. Organizar os estilos centrais.
6. Integrar tudo no `App.tsx`.
7. Implementar os métodos principais.
8. Validar o comportamento final da aplicação.

## 1. O que a correção precisa entregar

A solução final precisa atender ao enunciado da atividade:

- app em React Native com Expo;
- projeto sem Expo Router;
- tela única;
- componentes em `src/components/`;
- pelo menos quatro componentes reutilizáveis com `props` tipadas;
- formulário controlado;
- seletor da condição da entrega;
- resumo em tempo real;
- validação no envio;
- botão para registrar;
- botão para limpar;
- rodapé com nome e matrícula do aluno.

## 2. Criação do projeto e da estrutura

Para iniciar o projeto, a sequência sugerida é:

```bash
npx create-expo-app vistoria-rapida --template blank-typescript
cd vistoria-rapida
mkdir -p src/components
```

Depois disso, crie os seguintes arquivos:

```text
App.tsx
src/
  components/
    BotaoAcao.tsx
    CabecalhoApp.tsx
    CampoFormulario.tsx
    ResumoVistoria.tsx
    SeletorCondicao.tsx
  styles.ts
  types.ts
```

## 3. Estratégia de implementação

Nesta correção, vamos seguir a divisão abaixo:

- `App.tsx`: concentra estado, validação e integração da tela;
- `types.ts`: centraliza os tipos compartilhados;
- `styles.ts`: centraliza a aparência;
- `CabecalhoApp`: mostra o topo da tela;
- `CampoFormulario`: reaproveita a lógica visual dos campos;
- `SeletorCondicao`: alterna entre entrega íntegra e entrega com avaria;
- `ResumoVistoria`: mostra o resumo em tempo real e o último registro salvo;
- `BotaoAcao`: padroniza os botões principais.

Essa organização mantém o código legível e atende ao requisito de componentização.

## 4. Arquivo `src/types.ts`

Comece definindo os tipos principais da aplicação.

```tsx
export type CondicaoEntrega = 'integra' | 'avaria';

export type FormData = {
  responsavel: string;
  email: string;
  codigoPedido: string;
  bairroEntrega: string;
  quantidadeVolumes: string;
  observacao: string;
};

export type FormErrors = Partial<Record<keyof FormData, string>>;

export type ResumoRegistrado = {
  responsavel: string;
  email: string;
  codigoPedido: string;
  bairroEntrega: string;
  quantidadeVolumes: string;
  observacao: string;
  condicao: CondicaoEntrega;
};
```

### Leitura do Código

1. `CondicaoEntrega`: limita os valores possíveis para a condição da entrega.
2. `FormData`: representa todos os campos do formulário.
3. `FormErrors`: mapeia erros por campo sem obrigar todos os erros existirem ao mesmo tempo.
4. `ResumoRegistrado`: guarda os dados do último envio válido.

## 5. Arquivo `src/styles.ts`

Agora crie um arquivo único de estilos para simplificar a correção.

```tsx
import { StyleSheet } from 'react-native';

export const styles = StyleSheet.create({
  safeArea: {
    flex: 1,
    backgroundColor: '#e2e8f0',
  },
  container: {
    flexGrow: 1,
    padding: 16,
    backgroundColor: '#f8fafc',
    gap: 12,
  },
  card: {
    backgroundColor: '#ffffff',
    borderRadius: 16,
    padding: 16,
    gap: 12,
    shadowColor: '#0f172a',
    shadowOpacity: 0.08,
    shadowRadius: 12,
    shadowOffset: { width: 0, height: 6 },
    elevation: 3,
  },
  appTitle: {
    fontSize: 24,
    fontWeight: '700',
    color: '#0f172a',
  },
  appSubtitle: {
    fontSize: 14,
    lineHeight: 20,
    color: '#475569',
  },
  sectionTitle: {
    fontSize: 18,
    fontWeight: '700',
    color: '#0f172a',
  },
  fieldGroup: {
    gap: 6,
  },
  label: {
    fontSize: 14,
    fontWeight: '600',
    color: '#334155',
  },
  input: {
    borderWidth: 1,
    borderColor: '#cbd5e1',
    borderRadius: 12,
    backgroundColor: '#ffffff',
    paddingHorizontal: 12,
    paddingVertical: 12,
    fontSize: 15,
    color: '#0f172a',
  },
  inputError: {
    borderColor: '#dc2626',
  },
  multilineInput: {
    minHeight: 96,
    textAlignVertical: 'top',
  },
  errorText: {
    color: '#dc2626',
    fontSize: 12,
  },
  selectorOptions: {
    flexDirection: 'row',
    gap: 12,
  },
  selectorButton: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
    paddingHorizontal: 12,
    paddingVertical: 14,
    borderRadius: 12,
    borderWidth: 1,
    borderColor: '#cbd5e1',
    backgroundColor: '#ffffff',
  },
  selectorButtonActive: {
    borderColor: '#0f766e',
    backgroundColor: '#ccfbf1',
  },
  selectorButtonText: {
    color: '#334155',
    fontWeight: '600',
  },
  selectorButtonTextActive: {
    color: '#115e59',
  },
  summaryRow: {
    flexDirection: 'row',
    justifyContent: 'space-between',
    gap: 12,
  },
  summaryLabel: {
    flex: 1,
    color: '#475569',
    fontSize: 13,
  },
  summaryValue: {
    flex: 1,
    color: '#0f172a',
    fontWeight: '600',
    textAlign: 'right',
  },
  statusBadge: {
    alignSelf: 'flex-start',
    borderRadius: 999,
    backgroundColor: '#e0f2fe',
    paddingHorizontal: 12,
    paddingVertical: 6,
  },
  statusText: {
    color: '#075985',
    fontSize: 12,
    fontWeight: '700',
  },
  divider: {
    height: 1,
    backgroundColor: '#e2e8f0',
    marginVertical: 4,
  },
  buttonRow: {
    flexDirection: 'row',
    gap: 12,
  },
  button: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
    paddingVertical: 14,
    borderRadius: 12,
  },
  buttonPrimary: {
    backgroundColor: '#0f766e',
  },
  buttonSecondary: {
    backgroundColor: '#e2e8f0',
  },
  buttonTextPrimary: {
    color: '#ffffff',
    fontWeight: '700',
  },
  buttonTextSecondary: {
    color: '#0f172a',
    fontWeight: '700',
  },
  footer: {
    alignItems: 'center',
    gap: 4,
    paddingVertical: 8,
  },
  footerText: {
    color: '#475569',
    fontSize: 12,
  },
});
```

### Papel deste arquivo

- evitar repetição de estilos nos componentes;
- padronizar espaçamento, cores e bordas;
- facilitar futuras mudanças visuais.

## 6. Arquivo `src/components/CabecalhoApp.tsx`

Primeiro componente visual da tela:

```tsx
import { Text, View } from 'react-native';
import { styles } from '../styles';

type CabecalhoAppProps = {
  titulo: string;
  subtitulo: string;
};

export function CabecalhoApp({ titulo, subtitulo }: CabecalhoAppProps) {
  return (
    <View style={styles.card}>
      <Text style={styles.appTitle}>{titulo}</Text>
      <Text style={styles.appSubtitle}>{subtitulo}</Text>
    </View>
  );
}
```

### O que este componente resolve

- isola o cabeçalho;
- usa `props` tipadas;
- permite reutilização em outra tela, se necessário.

## 7. Arquivo `src/components/CampoFormulario.tsx`

Agora criamos o componente mais reaproveitável da atividade.

```tsx
import { Text, TextInput, type TextInputProps, View } from 'react-native';
import { styles } from '../styles';

type CampoFormularioProps = {
  label: string;
  value: string;
  placeholder: string;
  error?: string;
  keyboardType?: TextInputProps['keyboardType'];
  multiline?: boolean;
  numberOfLines?: number;
  onChangeText: (value: string) => void;
};

export function CampoFormulario({
  label,
  value,
  placeholder,
  error,
  keyboardType = 'default',
  multiline = false,
  numberOfLines,
  onChangeText,
}: CampoFormularioProps) {
  return (
    <View style={styles.fieldGroup}>
      <Text style={styles.label}>{label}</Text>

      <TextInput
        style={[
          styles.input,
          multiline && styles.multilineInput,
          error && styles.inputError,
        ]}
        value={value}
        placeholder={placeholder}
        keyboardType={keyboardType}
        multiline={multiline}
        numberOfLines={numberOfLines}
        onChangeText={onChangeText}
      />

      {error ? <Text style={styles.errorText}>{error}</Text> : null}
    </View>
  );
}
```

### O que este componente resolve

- reduz repetição de `TextInput`;
- centraliza o comportamento visual de erro;
- permite uso com campo simples ou multilinha.

## 8. Arquivo `src/components/SeletorCondicao.tsx`

O enunciado exige um controle por toque para a condição da entrega.

```tsx
import { Pressable, Text, View } from 'react-native';
import { styles } from '../styles';
import type { CondicaoEntrega } from '../types';

type SeletorCondicaoProps = {
  condicao: CondicaoEntrega;
  onChange: (condicao: CondicaoEntrega) => void;
};

export function SeletorCondicao({
  condicao,
  onChange,
}: SeletorCondicaoProps) {
  const opcoes: Array<{ label: string; value: CondicaoEntrega }> = [
    { label: 'Entrega integra', value: 'integra' },
    { label: 'Entrega com avaria', value: 'avaria' },
  ];

  return (
    <View style={styles.fieldGroup}>
      <Text style={styles.label}>Condicao da entrega</Text>

      <View style={styles.selectorOptions}>
        {opcoes.map((opcao) => {
          const ativa = condicao === opcao.value;

          return (
            <Pressable
              key={opcao.value}
              style={[
                styles.selectorButton,
                ativa && styles.selectorButtonActive,
              ]}
              onPress={() => onChange(opcao.value)}
            >
              <Text
                style={[
                  styles.selectorButtonText,
                  ativa && styles.selectorButtonTextActive,
                ]}
              >
                {opcao.label}
              </Text>
            </Pressable>
          );
        })}
      </View>
    </View>
  );
}
```

### O que este componente resolve

- deixa explícita a escolha por toque;
- mostra visualmente qual opção está ativa;
- evita espalhar lógica de `Pressable` pelo `App.tsx`.

## 9. Arquivo `src/components/BotaoAcao.tsx`

Os botões da tela também podem ser reaproveitados.

```tsx
import { Pressable, Text } from 'react-native';
import { styles } from '../styles';

type BotaoAcaoProps = {
  label: string;
  variant?: 'primary' | 'secondary';
  onPress: () => void;
};

export function BotaoAcao({
  label,
  variant = 'primary',
  onPress,
}: BotaoAcaoProps) {
  const buttonStyle =
    variant === 'primary' ? styles.buttonPrimary : styles.buttonSecondary;

  const textStyle =
    variant === 'primary'
      ? styles.buttonTextPrimary
      : styles.buttonTextSecondary;

  return (
    <Pressable style={[styles.button, buttonStyle]} onPress={onPress}>
      <Text style={textStyle}>{label}</Text>
    </Pressable>
  );
}
```

### O que este componente resolve

- padroniza botões da tela;
- diferencia ação principal e secundária;
- simplifica o bloco final do formulário.

## 10. Arquivo `src/components/ResumoVistoria.tsx`

Este componente atende ao resumo em tempo real e ao resumo da vistoria registrada.

```tsx
import { Text, View } from 'react-native';
import { styles } from '../styles';
import type { CondicaoEntrega, ResumoRegistrado } from '../types';

type ResumoVistoriaProps = {
  responsavel: string;
  codigoPedido: string;
  quantidadeVolumes: string;
  condicao: CondicaoEntrega;
  situacaoGeral: string;
  resumoRegistrado: ResumoRegistrado | null;
};

function formatarCondicao(condicao: CondicaoEntrega) {
  return condicao === 'integra' ? 'Entrega integra' : 'Entrega com avaria';
}

export function ResumoVistoria({
  responsavel,
  codigoPedido,
  quantidadeVolumes,
  condicao,
  situacaoGeral,
  resumoRegistrado,
}: ResumoVistoriaProps) {
  return (
    <View style={styles.card}>
      <Text style={styles.sectionTitle}>Resumo da vistoria</Text>

      <View style={styles.summaryRow}>
        <Text style={styles.summaryLabel}>Responsavel</Text>
        <Text style={styles.summaryValue}>
          {responsavel.trim() || 'Nao informado'}
        </Text>
      </View>

      <View style={styles.summaryRow}>
        <Text style={styles.summaryLabel}>Codigo do pedido</Text>
        <Text style={styles.summaryValue}>
          {codigoPedido.trim() || 'Nao informado'}
        </Text>
      </View>

      <View style={styles.summaryRow}>
        <Text style={styles.summaryLabel}>Quantidade de volumes</Text>
        <Text style={styles.summaryValue}>
          {quantidadeVolumes.trim() || 'Nao informada'}
        </Text>
      </View>

      <View style={styles.summaryRow}>
        <Text style={styles.summaryLabel}>Condicao selecionada</Text>
        <Text style={styles.summaryValue}>{formatarCondicao(condicao)}</Text>
      </View>

      <View style={styles.statusBadge}>
        <Text style={styles.statusText}>{situacaoGeral}</Text>
      </View>

      {resumoRegistrado ? (
        <>
          <View style={styles.divider} />
          <Text style={styles.sectionTitle}>Ultima vistoria registrada</Text>

          <View style={styles.summaryRow}>
            <Text style={styles.summaryLabel}>Responsavel</Text>
            <Text style={styles.summaryValue}>
              {resumoRegistrado.responsavel}
            </Text>
          </View>

          <View style={styles.summaryRow}>
            <Text style={styles.summaryLabel}>Pedido</Text>
            <Text style={styles.summaryValue}>
              {resumoRegistrado.codigoPedido}
            </Text>
          </View>

          <View style={styles.summaryRow}>
            <Text style={styles.summaryLabel}>Volumes</Text>
            <Text style={styles.summaryValue}>
              {resumoRegistrado.quantidadeVolumes}
            </Text>
          </View>

          <View style={styles.summaryRow}>
            <Text style={styles.summaryLabel}>Condicao</Text>
            <Text style={styles.summaryValue}>
              {formatarCondicao(resumoRegistrado.condicao)}
            </Text>
          </View>
        </>
      ) : null}
    </View>
  );
}
```

### O que este componente resolve

- mostra o painel em tempo real exigido no enunciado;
- mantém visível o último registro válido;
- evita poluir `App.tsx` com marcação repetitiva.

## 11. Arquivo `App.tsx`

Agora vem a parte principal: integrar os componentes e implementar os métodos da atividade.

```tsx
import { useState } from 'react';
import { Alert, SafeAreaView, ScrollView, Text, View } from 'react-native';
import { BotaoAcao } from './src/components/BotaoAcao';
import { CabecalhoApp } from './src/components/CabecalhoApp';
import { CampoFormulario } from './src/components/CampoFormulario';
import { ResumoVistoria } from './src/components/ResumoVistoria';
import { SeletorCondicao } from './src/components/SeletorCondicao';
import { styles } from './src/styles';
import type {
  CondicaoEntrega,
  FormData,
  FormErrors,
  ResumoRegistrado,
} from './src/types';

const initialForm: FormData = {
  responsavel: '',
  email: '',
  codigoPedido: '',
  bairroEntrega: '',
  quantidadeVolumes: '',
  observacao: '',
};

function validar(dados: FormData, condicao: CondicaoEntrega): FormErrors {
  const erros: FormErrors = {};

  if (!dados.responsavel.trim() || dados.responsavel.trim().length < 3) {
    erros.responsavel = 'Informe o responsavel com pelo menos 3 caracteres.';
  }

  if (!dados.email.includes('@') || !dados.email.includes('.')) {
    erros.email = 'Informe um e-mail valido.';
  }

  if (!dados.codigoPedido.trim() || dados.codigoPedido.trim().length < 5) {
    erros.codigoPedido =
      'Informe o codigo do pedido com pelo menos 5 caracteres.';
  }

  if (!dados.bairroEntrega.trim()) {
    erros.bairroEntrega = 'Informe o bairro de entrega.';
  }

  const volumes = Number(dados.quantidadeVolumes);
  if (!dados.quantidadeVolumes || Number.isNaN(volumes) || volumes <= 0) {
    erros.quantidadeVolumes =
      'Informe uma quantidade de volumes maior que zero.';
  }

  if (condicao === 'avaria') {
    if (!dados.observacao.trim() || dados.observacao.trim().length < 10) {
      erros.observacao =
        'Descreva a avaria com pelo menos 10 caracteres.';
    }
  }

  return erros;
}

function obterSituacaoGeral(
  dados: FormData,
  condicao: CondicaoEntrega,
  errosAtuais: FormErrors
) {
  const camposBasePreenchidos =
    dados.responsavel.trim() &&
    dados.codigoPedido.trim() &&
    dados.quantidadeVolumes.trim();

  if (!camposBasePreenchidos) {
    return 'Aguardando preenchimento';
  }

  if (condicao === 'avaria' && !dados.observacao.trim()) {
    return 'Revisar dados';
  }

  if (Object.keys(errosAtuais).length > 0) {
    return 'Revisar dados';
  }

  return 'Vistoria pronta para envio';
}

export default function App() {
  const [form, setForm] = useState<FormData>(initialForm);
  const [erros, setErros] = useState<FormErrors>({});
  const [condicao, setCondicao] = useState<CondicaoEntrega>('integra');
  const [resumoRegistrado, setResumoRegistrado] =
    useState<ResumoRegistrado | null>(null);

  function limparErroDoCampo(campo: keyof FormData) {
    setErros((estadoAnterior) => {
      if (!estadoAnterior[campo]) {
        return estadoAnterior;
      }

      const novosErros = { ...estadoAnterior };
      delete novosErros[campo];
      return novosErros;
    });
  }

  function atualizarCampo(campo: keyof FormData, valor: string) {
    setForm((estadoAnterior) => ({
      ...estadoAnterior,
      [campo]: valor,
    }));

    limparErroDoCampo(campo);
  }

  function atualizarQuantidadeVolumes(valor: string) {
    const apenasDigitos = valor.replace(/\D/g, '');
    atualizarCampo('quantidadeVolumes', apenasDigitos);
  }

  function alterarCondicao(novaCondicao: CondicaoEntrega) {
    setCondicao(novaCondicao);

    if (novaCondicao === 'integra') {
      setErros((estadoAnterior) => {
        if (!estadoAnterior.observacao) {
          return estadoAnterior;
        }

        const novosErros = { ...estadoAnterior };
        delete novosErros.observacao;
        return novosErros;
      });
    }
  }

  function registrarVistoria() {
    const errosEncontrados = validar(form, condicao);
    setErros(errosEncontrados);

    if (Object.keys(errosEncontrados).length > 0) {
      return;
    }

    const resumo: ResumoRegistrado = {
      ...form,
      condicao,
    };

    setResumoRegistrado(resumo);

    Alert.alert(
      'Vistoria registrada',
      `Pedido ${form.codigoPedido} registrado com sucesso.`
    );
  }

  function limparFormulario() {
    setForm(initialForm);
    setErros({});
    setCondicao('integra');
    setResumoRegistrado(null);
  }

  const errosAtuais = validar(form, condicao);
  const situacaoGeral = obterSituacaoGeral(form, condicao, errosAtuais);

  return (
    <SafeAreaView style={styles.safeArea}>
      <ScrollView
        contentContainerStyle={styles.container}
        keyboardShouldPersistTaps="handled"
      >
        <CabecalhoApp
          titulo="Vistoria Rapida"
          subtitulo="Registre rapidamente a condicao de entrega do pedido em campo."
        />

        <View style={styles.card}>
          <Text style={styles.sectionTitle}>Formulario de vistoria</Text>

          <CampoFormulario
            label="Responsavel"
            value={form.responsavel}
            placeholder="Nome do responsavel"
            error={erros.responsavel}
            onChangeText={(texto) => atualizarCampo('responsavel', texto)}
          />

          <CampoFormulario
            label="E-mail"
            value={form.email}
            placeholder="responsavel@empresa.com"
            keyboardType="email-address"
            error={erros.email}
            onChangeText={(texto) => atualizarCampo('email', texto)}
          />

          <CampoFormulario
            label="Codigo do pedido"
            value={form.codigoPedido}
            placeholder="Ex.: PED12345"
            error={erros.codigoPedido}
            onChangeText={(texto) => atualizarCampo('codigoPedido', texto)}
          />

          <CampoFormulario
            label="Bairro de entrega"
            value={form.bairroEntrega}
            placeholder="Informe o bairro"
            error={erros.bairroEntrega}
            onChangeText={(texto) => atualizarCampo('bairroEntrega', texto)}
          />

          <CampoFormulario
            label="Quantidade de volumes"
            value={form.quantidadeVolumes}
            placeholder="0"
            keyboardType="numeric"
            error={erros.quantidadeVolumes}
            onChangeText={atualizarQuantidadeVolumes}
          />

          <SeletorCondicao
            condicao={condicao}
            onChange={alterarCondicao}
          />

          <CampoFormulario
            label="Observacao"
            value={form.observacao}
            placeholder="Descreva a avaria quando necessario"
            multiline
            numberOfLines={4}
            error={erros.observacao}
            onChangeText={(texto) => atualizarCampo('observacao', texto)}
          />

          <View style={styles.buttonRow}>
            <BotaoAcao label="Registrar vistoria" onPress={registrarVistoria} />
            <BotaoAcao
              label="Limpar"
              variant="secondary"
              onPress={limparFormulario}
            />
          </View>
        </View>

        <ResumoVistoria
          responsavel={form.responsavel}
          codigoPedido={form.codigoPedido}
          quantidadeVolumes={form.quantidadeVolumes}
          condicao={condicao}
          situacaoGeral={situacaoGeral}
          resumoRegistrado={resumoRegistrado}
        />

        <View style={styles.card}>
          <View style={styles.footer}>
            <Text style={styles.footerText}>Aluno: Seu Nome Aqui</Text>
            <Text style={styles.footerText}>Matricula: 20250000000</Text>
          </View>
        </View>
      </ScrollView>
    </SafeAreaView>
  );
}
```

## 12. Métodos principais implementados no `App.tsx`

### `validar`

Responsável por verificar todas as regras do enunciado:

- responsável com pelo menos 3 caracteres;
- e-mail contendo `@` e `.`;
- código do pedido com pelo menos 5 caracteres;
- bairro obrigatório;
- quantidade de volumes maior que zero;
- observação obrigatória em caso de avaria.

### `obterSituacaoGeral`

Gera o texto do painel de status:

- `Aguardando preenchimento`;
- `Revisar dados`;
- `Vistoria pronta para envio`.

### `atualizarCampo`

Atualiza qualquer campo textual do formulário e remove o erro antigo daquele campo.

### `atualizarQuantidadeVolumes`

Limpa a entrada para manter apenas números em `quantidadeVolumes`.

### `alterarCondicao`

Troca a condição da entrega e remove o erro de observação quando a condição volta para `integra`.

### `registrarVistoria`

Executa o fluxo principal:

1. valida o formulário;
2. salva os erros;
3. interrompe o envio se houver inconsistências;
4. cria o resumo da vistoria registrada;
5. mostra uma confirmação com `Alert.alert`.

### `limparFormulario`

Restaura o estado inicial do app:

- limpa o formulário;
- limpa os erros;
- volta a condição para `integra`;
- remove o resumo registrado.

## 13. Ordem sugerida para montar a solução em sala

Se a correção for feita ao vivo, a sequência mais didática é:

1. criar `src/types.ts`;
2. criar `src/styles.ts`;
3. montar `CabecalhoApp`;
4. montar `CampoFormulario`;
5. montar `SeletorCondicao`;
6. montar `BotaoAcao`;
7. montar `ResumoVistoria`;
8. integrar no `App.tsx`;
9. implementar `validar`;
10. implementar `registrarVistoria`;
11. implementar `limparFormulario`;
12. testar os cenários do enunciado.

## 14. Checklist da correção funcional

- `App.tsx` continua como ponto de entrada principal;
- há pelo menos cinco componentes reutilizáveis;
- todos os componentes usam `props` tipadas;
- o formulário é totalmente controlado por estado;
- a condição da entrega muda por toque;
- a observação só é obrigatória em caso de avaria;
- o resumo muda em tempo real;
- o registro com sucesso funciona;
- o botão `Limpar` reseta toda a tela;
- nome e matrícula aparecem no rodapé.

## 15. Erros comuns que a correção evita

### Guardar lógica demais dentro do JSX

A correção separa a regra em funções nomeadas, o que torna a solução mais legível.

### Repetir `TextInput` manualmente

O componente `CampoFormulario` reduz repetição e melhora a manutenção.

### Validar apenas parte do enunciado

Nesta correção, todas as regras principais foram cobertas.

### Não manter o último resumo registrado

O estado `resumoRegistrado` garante esse comportamento pedido na atividade.

## 16. Encerramento

Esta correção entrega uma resposta completa e funcional para a atividade avaliativa, com foco em organização, legibilidade e aderência ao enunciado. Depois deste encontro, a trilha da disciplina retorna ao conteúdo planejado com **formulários controlados e máscaras**, no encontro 09.
