# API de Pruebas de Nombre de Usuario

## userNameProofByName

Obtén una prueba de nombre de usuario por el nombre de usuario de Farcaster

**Parámetros de Consulta**
| Parámetro | Descripción | Ejemplo |
| --------- | ----------- | ------- |
| name | El nombre de usuario de Farcaster o dirección ENS | `name=adityapk` O `name=dwr.eth` |

**Ejemplo**

```bash
curl http://127.0.0.1:2281/v1/userNameProofByName?name=adityapk
```

**Respuesta**

```json
{
  "timestamp": 1670603245,
  "name": "adityapk",
  "owner": "Oi7uUaECifDm+larm+rzl3qQhcM=",
  "signature": "fo5OhBP/ud...3IoJdhs=",
  "fid": 6833,
  "type": "USERNAME_TYPE_FNAME"
}
```

## userNameProofsByFid

Obtén una lista de pruebas proporcionadas por un FID

**Parámetros de Consulta**
| Parámetro | Descripción | Ejemplo |
| --------- | ----------- | ------- |
| fid | El FID solicitado | `fid=2` |

**Ejemplo**

```bash
curl http://127.0.0.1:2281/v1/userNameProofsByFid?fid=2
```

**Respuesta**

```json
{
  "proofs": [
    {
      "timestamp": 1623910393,
      "name": "v",
      "owner": "0x4114e33eb831858649ea3702e1c9a2db3f626446",
      "signature": "bANBae+Ub...kr3Bik4xs=",
      "fid": 2,
      "type": "USERNAME_TYPE_FNAME"
    },
    {
      "timestamp": 1690329118,
      "name": "varunsrin.eth",
      "owner": "0x182327170fc284caaa5b1bc3e3878233f529d741",
      "signature": "zCEszPt...zqxTiFqVBs=",
      "fid": 2,
      "type": "USERNAME_TYPE_ENS_L1"
    }
  ]
}
```
