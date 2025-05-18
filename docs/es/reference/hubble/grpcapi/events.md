# API de Eventos

Se utiliza para suscribirse a actualizaciones de eventos en tiempo real desde el Hub de Farcaster.

## API

| Nombre del Método | Tipo de Solicitud | Tipo de Respuesta | Descripción                                   |
| ----------------- | ----------------- | ----------------- | --------------------------------------------- |
| Subscribe         | SubscribeRequest  | stream HubEvent   | Transmite nuevos Eventos a medida que ocurren |
| GetEvent          | EventRequest      | HubEvent          | Transmite nuevos Eventos a medida que ocurren |

## SubscribeRequest

| Campo       | Tipo           | Etiqueta | Descripción                                         |
| ----------- | -------------- | -------- | --------------------------------------------------- |
| event_types | [EventType](#) | repeated | Tipos de eventos a los que suscribirse              |
| from_id     | uint64         | optional | ID del Evento desde el cual comenzar la transmisión |

## EventRequest

| Campo | Tipo              | Etiqueta | Descripción |
| ----- | ----------------- | -------- | ----------- |
| id    | [uint64](#uint64) |          |             |
