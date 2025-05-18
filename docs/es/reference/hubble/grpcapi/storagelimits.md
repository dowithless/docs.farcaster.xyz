# API de Almacenamiento

Obtener los límites de almacenamiento de un FID.

## API

| Nombre del Método            | Tipo de Solicitud | Tipo de Respuesta     | Descripción                                                                      |
| ---------------------------- | ----------------- | --------------------- | -------------------------------------------------------------------------------- |
| GetCurrentStorageLimitsByFid | FidRequest        | StorageLimitsResponse | Devuelve los límites actuales de almacenamiento para todas las tiendas de un FID |

#### StorageLimitsResponse

| Campo  | Tipo              | Etiqueta | Descripción                                  |
| ------ | ----------------- | -------- | -------------------------------------------- |
| limits | [StorageLimit](#) | repeated | Límites de almacenamiento por tipo de tienda |

#### StorageLimit

| Campo      | Tipo           | Etiqueta | Descripción                                                        |
| ---------- | -------------- | -------- | ------------------------------------------------------------------ |
| store_type | [StoreType](#) |          | El tipo específico gestionado por la tienda                        |
| limit      | [uint64](#)    |          | El límite del tipo de tienda, escalado por el alquiler del usuario |
