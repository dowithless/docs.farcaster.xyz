---
outline: [2, 3]
---

# Registro de Almacenamiento

El Registro de Almacenamiento permite a las cuentas de Farcaster alquilar una o más "unidades" de almacenamiento en la red.

Si deseas alquilar almacenamiento para una cuenta de Farcaster, utiliza el Registro de Almacenamiento.

## Lectura

### unitPrice

Obtiene el precio en wei (`uint256`) para registrar 1 unidad de almacenamiento.

### price

Obtiene el precio en wei (`uint256`) para registrar un número específico de unidades de almacenamiento.

| Nombre del Parámetro | tipo      | Descripción                                        |
| -------------------- | --------- | -------------------------------------------------- |
| units                | `uint256` | El número de unidades de almacenamiento a alquilar |

## Escritura

### rent

Alquila un número específico de unidades de almacenamiento para un fid dado. El exceso de ether será devuelto al llamante. Las unidades alquiladas son válidas por 1 año a partir del momento del registro.

| Nombre del Parámetro | tipo      | Descripción                                                 |
| -------------------- | --------- | ----------------------------------------------------------- |
| `msg.value`          | `wei`     | Cantidad de pago                                            |
| fid                  | `uint256` | El fid al que se acreditarán las unidades de almacenamiento |
| units                | `uint256` | El número de unidades de almacenamiento a alquilar          |

### batchRent

Alquila almacenamiento para múltiples fids en una sola transacción. El llamante debe enviar suficiente ether para cubrir el costo total de todas las unidades. Al igual que el alquiler de una sola unidad, el ether adicional se devuelve y las unidades son válidas por 1 año.

| Nombre del Parámetro | tipo        | Descripción                                                                     |
| -------------------- | ----------- | ------------------------------------------------------------------------------- |
| `msg.value`          | `wei`       | Cantidad total de pago                                                          |
| fids                 | `uint256[]` | Array de fids                                                                   |
| units                | `uint256[]` | Array de cantidades de unidades, correspondientes a cada fid en el array `fids` |

## Errores

| Error             | Selector   | Descripción                                                                                                 |
| ----------------- | ---------- | ----------------------------------------------------------------------------------------------------------- |
| InvalidPayment    | `3c6b4b28` | El llamante no proporcionó suficiente ether para pagar el número de unidades de almacenamiento solicitadas. |
| InvalidBatchInput | `0a514b99` | El llamante proporcionó arrays de `fids` y `units` que no coinciden.                                        |

## Fuente

[`StorageRegistry.sol`](https://github.com/farcasterxyz/contracts/blob/1aceebe916de446f69b98ba1745a42f071785730/src/validators/StorageRegistry.sol)
