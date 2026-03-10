# Encontro 26 - Arquitetura, serviços e boas práticas de código

## Objetivos

- Organizar a aplicação em camadas.
- Separar tela, regra de negócio e acesso a dados.
- Reforçar convenções para manutenção.

## Explicação técnica

À medida que o app cresce, estrutura importa mais do que velocidade inicial. Este encontro é adequado para consolidar uma arquitetura simples, com pastas de telas, componentes, serviços, repositórios e utilitários. O foco não é formalismo excessivo, mas redução de acoplamento.

```text
src/
  components/
  screens/
  services/
  repositories/
  hooks/
  utils/
```

## Perguntas orientadoras

- Onde fica a chamada de API?
- Quem valida dados?
- A tela conhece SQL ou detalhes de rede?

## Atividade

- Refatorar um app da Unidade 2.
- Criar convenção de nomes e responsabilidades por pasta.

## Materiais complementares

- Clean architecture debates.
- React Native project structure references.
