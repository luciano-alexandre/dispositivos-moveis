# Encontro 24 - Localização: geocoding e mapas

## Objetivos

- Ler coordenadas do dispositivo.
- Converter coordenadas em endereços e vice-versa.
- Exibir informações geográficas em mapa.

## Explicação técnica

Localização é recurso poderoso, mas sensível. É necessário combinar precisão técnica com responsabilidade no tratamento de dados do usuário. O encontro deve abordar permissões, latitude/longitude, geocoding e limitações de precisão.

```tsx
const posicao = await Location.getCurrentPositionAsync({});
console.log(posicao.coords.latitude, posicao.coords.longitude);
```

## Aplicações para discutir

- entrega e rastreamento;
- localização de serviços próximos;
- registro de presença contextual.

## Atividade

- Capturar coordenadas atuais.
- Mostrar endereço aproximado.
- Marcar ponto em um mapa.

## Materiais complementares

- Expo Location: <https://docs.expo.dev/versions/latest/sdk/location/>
- React Native Maps: <https://github.com/react-native-maps/react-native-maps>
