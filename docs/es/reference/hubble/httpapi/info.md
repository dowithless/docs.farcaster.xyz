# API de Información

## info

Obtener la información del Hub

**Parámetros de Consulta**
| Parámetro | Descripción | Ejemplo |
| --------- | ----------- | ------- |
| dbstats | Si se deben devolver estadísticas de la base de datos | `dbstats=1` |

**Ejemplo**

```bash
curl http://127.0.0.1:2281/v1/info?dbstats=1

```

**Respuesta**

```json
{
  "version": "1.5.5",
  "isSyncing": false,
  "nickname": "Farcaster Hub",
  "rootHash": "fa349603a6c29d27041225261891bc9bc846bccb",
  "dbStats": {
    "numMessages": 4191203,
    "numFidEvents": 20287,
    "numFnameEvents": 20179
  },
  "peerId": "12D3KooWNr294AH1fviDQxRmQ4K79iFSGoRCWzGspVxPprJUKN47",
  "hubOperatorFid": 6833
}
```
