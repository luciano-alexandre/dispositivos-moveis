# Encontro 19 - HTTP, JSON e consumo de API REST

## Objetivos

- Entender requisições HTTP.
- Ler respostas JSON.
- Consumir dados remotos no app.

## Explicação técnica

Apps móveis frequentemente funcionam como clientes de APIs REST. O estudante deve entender verbo HTTP, status code, cabeçalhos e corpo da resposta. JSON é a estrutura de dados mais comum na troca entre app e servidor.

```tsx
async function carregarPosts() {
  const resposta = await fetch('https://jsonplaceholder.typicode.com/posts');
  const dados = await resposta.json();
  return dados;
}
```

## Pontos para explorar

- `GET`, `POST`, `PUT`, `DELETE`.
- `200`, `201`, `400`, `401`, `404`, `500`.
- Estrutura de array e objeto JSON.

## Atividade

- Buscar dados de uma API pública.
- Renderizar os resultados em lista.
- Exibir estados de carregando e erro.

## Materiais complementares

- MDN HTTP: <https://developer.mozilla.org/docs/Web/HTTP>
- JSON overview: <https://developer.mozilla.org/docs/Learn_web_development/Core/Scripting/JSON>
