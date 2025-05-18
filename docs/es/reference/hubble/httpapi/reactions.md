# API de Reacciones

La API de Reacciones aceptará los siguientes valores (ya sea la representación en cadena o el valor numérico) para el campo `reaction_type`.

| String               | Valor numérico | Descripción                                             |
| -------------------- | -------------- | ------------------------------------------------------- |
| REACTION_TYPE_LIKE   | 1              | Dar "me gusta" al cast objetivo                         |
| REACTION_TYPE_RECAST | 2              | Compartir el cast objetivo con la audiencia del usuario |

## reactionById

Obtener una reacción por su FID creador y el Cast objetivo.

**Parámetros de consulta**
| Parámetro | Descripción | Ejemplo |
| --------- | ----------- | ------- |
| fid | El FID del creador de la reacción | `fid=6833` |
| target_fid | El FID del creador del cast | `target_fid=2` |
| target_hash | El hash del cast | `target_hash=0xa48dd46161d8e57725f5e26e34ec19c13ff7f3b9` |
| reaction_type | El tipo de reacción, ya sea como valor numérico de enumeración o representación en cadena | `reaction_type=1` O `reaction_type=REACTION_TYPE_LIKE` |

**Ejemplo**

```bash
curl http://127.0.0.1:2281/v1/reactionById?fid=2&reaction_type=1&target_fid=1795&target_hash=0x7363f449bfb0e7f01c5a1cc0054768ed5146abc0
```

**Respuesta**

```json
{
  "data": {
    "type": "MESSAGE_TYPE_REACTION_ADD",
    "fid": 2,
    "timestamp": 72752656,
    "network": "FARCASTER_NETWORK_MAINNET",
    "reactionBody": {
      "type": "REACTION_TYPE_LIKE",
      "targetCastId": {
        "fid": 1795,
        "hash": "0x7363f449bfb0e7f01c5a1cc0054768ed5146abc0"
      }
    }
  },
  "hash": "0x9fc9c51f6ea3acb84184efa88ba4f02e7d161766",
  "hashScheme": "HASH_SCHEME_BLAKE3",
  "signature": "F2OzKsn6Wj...gtyORbyCQ==",
  "signatureScheme": "SIGNATURE_SCHEME_ED25519",
  "signer": "0x78ff9a7...647b6d62558c"
}
```

## reactionsByFid

Obtener todas las reacciones por un FID

**Parámetros de consulta**
| Parámetro | Descripción | Ejemplo |
| --------- | ----------- | ------- |
| fid | El FID del creador de la reacción | `fid=6833` |
| reaction_type | El tipo de reacción, ya sea como valor numérico de enumeración o representación en cadena | `reaction_type=1` O `reaction_type=REACTION_TYPE_LIKE` |

**Ejemplo**

```bash
curl http://127.0.0.1:2281/v1/reactionsByFid?fid=2&reaction_type=1
```

**Respuesta**

```json
{
  "messages": [
    {
      "data": {
        "type": "MESSAGE_TYPE_REACTION_ADD",
        "fid": 2,
        "timestamp": 72752656,
        "network": "FARCASTER_NETWORK_MAINNET",
        "reactionBody": {
          "type": "REACTION_TYPE_LIKE",
          "targetCastId": {
            "fid": 1795,
            "hash": "0x7363f449bfb0e7f01c5a1cc0054768ed5146abc0"
          }
        }
      },
      "hash": "0x9fc9c51f6ea3acb84184efa88ba4f02e7d161766",
      "hashScheme": "HASH_SCHEME_BLAKE3",
      "signature": "F2OzKsn6WjP8MTw...hqUbrAvp6mggtyORbyCQ==",
      "signatureScheme": "SIGNATURE_SCHEME_ED25519",
      "signer": "0x78ff9a768...62558c"
    }
  ],
  "nextPageToken": ""
}
```

## reactionsByCast

Obtener todas las reacciones a un cast

**Parámetros de consulta**
| Parámetro | Descripción | Ejemplo |
| --------- | ----------- | ------- |
| target_fid | El FID del creador del cast | `fid=6833` |
| target_hash | El hash del cast | `target_hash=`0x7363f449bfb0e7f01c5a1cc0054768ed5146abc0`|
| reaction_type | El tipo de reacción, ya sea como valor numérico de enumeración o representación en cadena |`reaction_type=1`O`reaction_type=REACTION_TYPE_LIKE` |

**Ejemplo**

```bash
curl http://127.0.0.1:2281/v1/reactionsByCast?target_fid=2&reaction_type=1&target_hash=0x7363f449bfb0e7f01c5a1cc0054768ed5146abc0
```

**Respuesta**

```json
{
  "messages": [
    {
      "data": {
        "type": "MESSAGE_TYPE_REACTION_ADD",
        "fid": 426,
        "timestamp": 72750141,
        "network": "FARCASTER_NETWORK_MAINNET",
        "reactionBody": {
          "type": "REACTION_TYPE_LIKE",
          "targetCastId": {
            "fid": 1795,
            "hash": "0x7363f449bfb0e7f01c5a1cc0054768ed5146abc0"
          }
        }
      },
      "hash": "0x7662fba1be3166fc75acc0914a7b0e53468d5e7a",
      "hashScheme": "HASH_SCHEME_BLAKE3",
      "signature": "tmAUEYlt/+...R7IO3CA==",
      "signatureScheme": "SIGNATURE_SCHEME_ED25519",
      "signer": "0x13dd2...204e57bc2a"
    }
  ],
  "nextPageToken": ""
}
```

## reactionsByTarget

Obtener todas las reacciones a la URL objetivo de un cast

**Parámetros de consulta**
| Parámetro | Descripción | Ejemplo |
| --------- | ----------- | ------- |
| url | La URL del cast padre | url=chain://eip155:1/erc721:0x39d89b649ffa044383333d297e325d42d31329b2 |
| reaction_type | El tipo de reacción, ya sea como valor numérico de enumeración o representación en cadena | `reaction_type=1` O `reaction_type=REACTION_TYPE_LIKE` |

**Ejemplo**

```bash
curl http://127.0.0.1:2281/v1/reactionsByTarget?url=chain://eip155:1/erc721:0x39d89b649ffa044383333d297e325d42d31329b2
```

**Respuesta**

```json
{
  "messages": [
    {
      "data": {
        "type": "MESSAGE_TYPE_REACTION_ADD",
        "fid": 1134,
        "timestamp": 79752856,
        "network": "FARCASTER_NETWORK_MAINNET",
        "reactionBody": {
          "type": "REACTION_TYPE_LIKE",
          "targetUrl": "chain://eip155:1/erc721:0x39d89b649ffa044383333d297e325d42d31329b2"
        }
      },
      "hash": "0x94a0309cf11a07b95ace71c62837a8e61f17adfd",
      "hashScheme": "HASH_SCHEME_BLAKE3",
      "signature": "+f/+M...0Uqzd0Ag==",
      "signatureScheme": "SIGNATURE_SCHEME_ED25519",
      "signer": "0xf6...3769198d4c"
    }
  ],
  "nextPageToken": ""
}
```
