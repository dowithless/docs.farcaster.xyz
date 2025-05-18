# API de Eventos

La API de eventos devuelve eventos a medida que se fusionan en el Hub, lo que permite monitorear la actividad del Hub.

## eventById

Obtener un evento por su ID

**Parámetros de Consulta**
| Parámetro | Descripción | Ejemplo |
| --------- | ----------- | ------- |
| event_id | El ID del evento en el Hub | `event_id=350909155450880` |

**Ejemplo**

```bash
curl http://127.0.0.1:2281/v1/eventById?id=350909155450880

```

**Respuesta**

```json
{
  "type": "HUB_EVENT_TYPE_MERGE_USERNAME_PROOF",
  "id": 350909155450880,
  "mergeUsernameProofBody": {
    "usernameProof": {
      "timestamp": 1695049760,
      "name": "nftonyp",
      "owner": "0x23b3c29900762a70def5dc8890e09dc9019eb553",
      "signature": "xp41PgeO...hJpNshw=",
      "fid": 20114,
      "type": "USERNAME_TYPE_FNAME"
    }
  }
}
```

## events

Obtener una página de eventos del Hub

**Parámetros de Consulta**
| Parámetro | Descripción | Ejemplo |
| --------- | ----------- | ------- |
| from_event_id | Un ID opcional del Hub para comenzar a obtener eventos. También se devuelve desde la API como `nextPageEventId`, que puede usarse para navegar por todos los eventos del Hub. Establécelo en `0` para comenzar desde el primer evento | `from_event_id=350909155450880` |

**Nota**
Los Hubs eliminan eventos anteriores a 3 días, por lo que no todos los eventos históricos pueden recuperarse a través de esta API

**Ejemplo**

```bash
curl http://127.0.0.1:2281/v1/events?from_event_id=350909155450880

```

**Respuesta**

```json
{
  "nextPageEventId": 350909170294785,
  "events": [
    {
      "type": "HUB_EVENT_TYPE_MERGE_USERNAME_PROOF",
      "id": 350909155450880,
      "mergeUsernameProofBody": {
        "usernameProof": {
          "timestamp": 1695049760,
          "name": "nftonyp",
          "owner": "0x23b3c29900762a70def5dc8890e09dc9019eb553",
          "signature": "xp41PgeOz...9Jw5vT/eLnGphJpNshw=",
          "fid": 20114,
          "type": "USERNAME_TYPE_FNAME"
        }
      }
    },
    ...
  ]
}
```
