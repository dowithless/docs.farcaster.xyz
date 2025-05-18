# API de Fids

## fids

Obtén una lista de todos los FIDs

**Parámetros de Consulta**
| Parámetro | Descripción | Ejemplo |
| --------- | ----------- | ------- |
| | Este endpoint no acepta parámetros | |

**Ejemplo**

```bash
curl http://127.0.0.1:2281/v1/fids
```

**Respuesta**

```json
{
  "fids": [1, 2, 3, 4, 5, 6],
  "nextPageToken": "AAAnEA=="
}
```
