# Encontro 20 - Axios/fetch, tratamento de erros e loading

## Objetivos

- Comparar `fetch` e `axios`.
- Tratar estados de carregamento, sucesso e falha.
- Evitar interfaces bloqueadas.

## Explicação técnica

Consumir API sem tratamento adequado produz experiências ruins. Você precisa estruturar pelo menos três estados: carregando, dados disponíveis e erro. Isso é parte da qualidade da interface, não um detalhe opcional.

```tsx
const [dados, setDados] = useState<any[]>([]);
const [carregando, setCarregando] = useState(false);
const [erro, setErro] = useState('');
```

```tsx
try {
  setCarregando(true);
  const resposta = await fetch(url);
  const json = await resposta.json();
  setDados(json);
} catch (e) {
  setErro('Falha ao carregar dados');
} finally {
  setCarregando(false);
}
```

## Atividade

- Criar tela com indicador de loading.
- Simular erro de rede.
- Exibir ação de tentar novamente.

## Materiais complementares

- Fetch API: <https://developer.mozilla.org/docs/Web/API/Fetch_API>
- Axios docs: <https://axios-http.com/docs/intro>
