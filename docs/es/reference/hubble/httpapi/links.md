# API de Enlaces

Un Link (enlace) representa una relación entre dos usuarios (ej. seguimiento)

La API de Enlaces aceptará los siguientes valores para el campo `link_type`.

| String | Descripción                              |
| ------ | ---------------------------------------- |
| follow | Seguimiento desde FID hacia FID objetivo |

## linkById

Obtiene un enlace por su FID y FID objetivo.

**Parámetros de Consulta**
| Parámetro | Descripción | Ejemplo |
| --------- | ----------- | ------- |
| fid | El FID del originador del enlace | `fid=6833` |
| target_fid | El FID del objetivo del enlace | `target_fid=2` |
| link_type | El tipo de enlace, como valor string| `link_type=follow` |

**Ejemplo**

```bash
curl http://127.0.0.1:2281/v1/linkById?fid=6833&target_fid=2&link_type=follow
```

**Respuesta**

```json
{
  "data": {
    "type": "MESSAGE_TYPE_LINK_ADD",
    "fid": 6833,
    "timestamp": 61144470,
    "network": "FARCASTER_NETWORK_MAINNET",
    "linkBody": {
      "type": "follow",
      "targetFid": 2
    }
  },
  "hash": "0x58c23eaf4f6e597bf3af44303a041afe9732971b",
  "hashScheme": "HASH_SCHEME_BLAKE3",
  "signature": "sMypYEMqSyY...nfCA==",
  "signatureScheme": "SIGNATURE_SCHEME_ED25519",
  "signer": "0x0852c07b56...06e999cdd"
}
```

## linksByFid

Obtiene todos los enlaces desde un FID origen.

**Parámetros de Consulta**
| Parámetro | Descripción | Ejemplo |
| --------- | ----------- | ------- |
| fid | El FID del creador de la reacción | `fid=6833` |
| link_type | El tipo de enlace, como valor string| `link_type=follow` |

**Ejemplo**

```bash
curl http://127.0.0.1:2281/v1/linksByFid?fid=6833
```

**Respuesta**

```json
{
  "messages": [
    {
      "data": {
        "type": "MESSAGE_TYPE_LINK_ADD",
        "fid": 6833,
        "timestamp": 61144470,
        "network": "FARCASTER_NETWORK_MAINNET",
        "linkBody": {
          "type": "follow",
          "targetFid": 83
        }
      },
      "hash": "0x094e35891519c0e04791a6ba4d2eb63d17462f02",
      "hashScheme": "HASH_SCHEME_BLAKE3",
      "signature": "qYsfX08mS...McYq6IYMl+ECw==",
      "signatureScheme": "SIGNATURE_SCHEME_ED25519",
      "signer": "0x0852c0...a06e999cdd"
    }
  ],
  "nextPageToken": ""
}
```

## linksByTargetFid

Obtiene todos los enlaces hacia un FID objetivo.

**Parámetros de Consulta**
| Parámetro | Descripción | Ejemplo |
| --------- | ----------- | ------- |
| target_fid | El FID del creador de la reacción | `fid=6833` |
| link_type | El tipo de enlace, como valor string| `link_type=follow` |

**Ejemplo**

```bash
curl http://127.0.0.1:2281/v1/linksByTargetFid?target_fid=6833
```

**Respuesta**

```json
{
  "messages": [
    {
      "data": {
        "type": "MESSAGE_TYPE_LINK_ADD",
        "fid": 302,
        "timestamp": 61144668,
        "network": "FARCASTER_NETWORK_MAINNET",
        "linkBody": {
          "type": "follow",
          "targetFid": 6833
        }
      },
      "hash": "0x78c62531d96088f640ffe7e62088b49749efe286",
      "hashScheme": "HASH_SCHEME_BLAKE3",
      "signature": "frIZJGIizv...qQd9QJyCg==",
      "signatureScheme": "SIGNATURE_SCHEME_ED25519",
      "signer": "0x59a04...6860ddfab"
    }
  ],
  "nextPageToken": ""
}
```
