# API de Casts

## castById

Obtener un cast por su FID y Hash.

**Parámetros de Consulta**
| Parámetro | Descripción | Ejemplo |
| --------- | ----------- | ------- |
| fid | El FID del creador del cast | `fid=6833` |
| hash | El hash del cast | `hash=0xa48dd46161d8e57725f5e26e34ec19c13ff7f3b9` |

**Ejemplo**

```bash
curl http://127.0.0.1:2281/v1/castById?fid=2&hash=0xd2b1ddc6c88e865a33cb1a565e0058d757042974
```

**Respuesta**

```json
{
  "data": {
    "type": "MESSAGE_TYPE_CAST_ADD",
    "fid": 2,
    "timestamp": 48994466,
    "network": "FARCASTER_NETWORK_MAINNET",
    "castAddBody": {
      "embedsDeprecated": [],
      "mentions": [],
      "parentCastId": {
        "fid": 226,
        "hash": "0xa48dd46161d8e57725f5e26e34ec19c13ff7f3b9"
      },
      "text": "Texto del Cast",
      "mentionsPositions": [],
      "embeds": []
    }
  },
  "hash": "0xd2b1ddc6c88e865a33cb1a565e0058d757042974",
  "hashScheme": "HASH_SCHEME_BLAKE3",
  "signature": "3msLXzxB4eEYe...dHrY1vkxcPAA==",
  "signatureScheme": "SIGNATURE_SCHEME_ED25519",
  "signer": "0x78ff9a...58c"
}
```

## castsByFid

Obtener todos los casts creados por un FID.

**Parámetros de Consulta**
| Parámetro | Descripción | Ejemplo |
| --------- | ----------- | ------- |
| fid | El FID del creador del cast | `fid=6833` |

**Ejemplo**

```bash
curl http://127.0.0.1:2281/v1/castsByFid?fid=2
```

**Respuesta**

```json
{
  "messages": [
    {
      "data": {
        "type": "MESSAGE_TYPE_CAST_ADD",
        "fid": 2,
        "timestamp": 48994466,
        "network": "FARCASTER_NETWORK_MAINNET",
        "castAddBody": {... },
          "text": "Texto del Cast",
          "mentionsPositions": [],
          "embeds": []
        }
      },
      "hash": "0xd2b1ddc6c88e865a33cb1a565e0058d757042974",
      "hashScheme": "HASH_SCHEME_BLAKE3",
      "signature": "3msLXzxB4eEYeF0Le...dHrY1vkxcPAA==",
      "signatureScheme": "SIGNATURE_SCHEME_ED25519",
      "signer": "0x78ff9a768cf1...2eca647b6d62558c"
    }
  ]
  "nextPageToken": ""
}
```

## castsByParent

Obtener todos los casts por el FID y Hash del cast padre O por la URL del padre

**Parámetros de Consulta**
| Parámetro | Descripción | Ejemplo |
| --------- | ----------- | ------- |
| fid | El FID del cast padre | `fid=6833` |
| hash | El hash del cast padre | `hash=0xa48dd46161d8e57725f5e26e34ec19c13ff7f3b9` |
| url | La URL del cast padre | `url=chain://eip155:1/erc721:0x39d89b649ffa044383333d297e325d42d31329b2` |

**Nota**
Puedes usar `?fid=...&hash=...` O `?url=...` para consultar este endpoint

**Ejemplo**

```bash
curl http://127.0.0.1:2281/v1/castsByParent?fid=226&hash=0xa48dd46161d8e57725f5e26e34ec19c13ff7f3b9
```

**Respuesta**

```json
{
  "messages": [
    {
      "data": {
        "type": "MESSAGE_TYPE_CAST_ADD",
        "fid": 226,
        "timestamp": 48989255,
        "network": "FARCASTER_NETWORK_MAINNET",
        "castAddBody": {
          "embedsDeprecated": [],
          "mentions": [],
          "parentCastId": {
            "fid": 226,
            "hash": "0xa48dd46161d8e57725f5e26e34ec19c13ff7f3b9"
          },
          "text": "Texto del Cast",
          "mentionsPositions": [],
          "embeds": []
        }
      },
      "hash": "0x0e501b359f88dcbcddac50a8f189260a9d02ad34",
      "hashScheme": "HASH_SCHEME_BLAKE3",
      "signature": "MjKnOQCTW42K8+A...tRbJfia2JJBg==",
      "signatureScheme": "SIGNATURE_SCHEME_ED25519",
      "signer": "0x6f1e8758...7f04a3b500ba"
    }
  ],
  "nextPageToken": ""
}
```

## castsByMention

Obtener todos los casts que mencionan un FID

**Parámetros de Consulta**
| Parámetro | Descripción | Ejemplo |
| --------- | ----------- | ------- |
| fid | El FID que es mencionado en un cast | `fid=6833` |

**Nota**
Usa `mentionsPositions` para extraer la posición en el texto del cast donde se mencionó el FID

**Ejemplo**

```bash
curl http://127.0.0.1:2281/v1/castsByMention?fid=6833
```

**Respuesta**

```json
{
  "messages": [
    {
      "data": {
        "type": "MESSAGE_TYPE_CAST_ADD",
        "fid": 2,
        "timestamp": 62298143,
        "network": "FARCASTER_NETWORK_MAINNET",
        "castAddBody": {
          "embedsDeprecated": [],
          "mentions": [15, 6833],
          "parentCastId": {
            "fid": 2,
            "hash": "0xd5540928cd3daf2758e501a61663427e41dcc09a"
          },
          "text": "cc  y ",
          "mentionsPositions": [3, 8],
          "embeds": []
        }
      },
      "hash": "0xc6d4607835197a8ee225e9218d41e38aafb12076",
      "hashScheme": "HASH_SCHEME_BLAKE3",
      "signature": "TOaWrSTmz+cyzPMFGvF...OeUznB0Ag==",
      "signatureScheme": "SIGNATURE_SCHEME_ED25519",
      "signer": "0x78ff9a768c...647b6d62558c"
    }
  ],
  "nextPageToken": ""
}
```
