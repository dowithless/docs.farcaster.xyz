# API de Almacenamiento

## storageLimitsByFid

Obtiene los límites de almacenamiento de un FID.

**Parámetros de Consulta**
| Parámetro | Descripción | Ejemplo |
| --------- | ----------- | ------- |
| fid | El FID solicitado | `fid=6833` |

**Ejemplo**

```bash
curl http://127.0.0.1:2281/v1/storageLimitsByFid?fid=6833
```

**Respuesta**

```json
{
  "limits": [
    {
      "storeType": "STORE_TYPE_CASTS",
      "limit": 10000
    },
    {
      "storeType": "STORE_TYPE_LINKS",
      "limit": 5000
    },
    {
      "storeType": "STORE_TYPE_REACTIONS",
      "limit": 5000
    },
    {
      "storeType": "STORE_TYPE_USER_DATA",
      "limit": 100
    },
    {
      "storeType": "STORE_TYPE_USERNAME_PROOFS",
      "limit": 10
    },
    {
      "storeType": "STORE_TYPE_VERIFICATIONS",
      "limit": 50
    }
  ]
}
```
