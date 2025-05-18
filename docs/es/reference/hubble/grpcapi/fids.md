# API de Fids

Se utiliza para recuperar una lista de todos los fids

## API

| Nombre del Método | Tipo de Solicitud | Tipo de Respuesta | Descripción                                   |
| ----------------- | ----------------- | ----------------- | --------------------------------------------- |
| GetFids           | FidsRequest       | FidsResponse      | Devuelve una lista paginada de todos los fids |

## FidsRequest

| Campo      | Tipo              | Etiqueta | Descripción |
| ---------- | ----------------- | -------- | ----------- |
| page_size  | [uint32](#uint32) | opcional |             |
| page_token | [bytes](#bytes)   | opcional |             |
| reverse    | [bool](#bool)     | opcional |             |

## FidsResponse

| Campo           | Tipo            | Etiqueta | Descripción   |
| --------------- | --------------- | -------- | ------------- |
| fids            | [uint64](#)     | repetido | Array de fids |
| next_page_token | [bytes](#bytes) | opcional |               |
