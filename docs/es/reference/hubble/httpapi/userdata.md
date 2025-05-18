# API de UserData

La API de UserData aceptará los siguientes valores para el campo `user_data_type`.

| String                  | Valor numérico | Descripción                     |
| ----------------------- | -------------- | ------------------------------- |
| USER_DATA_TYPE_PFP      | 1              | Foto de perfil del usuario      |
| USER_DATA_TYPE_DISPLAY  | 2              | Nombre para mostrar del usuario |
| USER_DATA_TYPE_BIO      | 3              | Biografía del usuario           |
| USER_DATA_TYPE_URL      | 5              | URL del usuario                 |
| USER_DATA_TYPE_USERNAME | 6              | Nombre preferido del usuario    |
| USER_DATA_TYPE_LOCATION | 7              | Ubicación del usuario           |
| USER_DATA_TYPE_TWITTER  | 8              | Nombre de usuario de Twitter    |
| USER_DATA_TYPE_GITHUB   | 9              | Nombre de usuario de GitHub     |

Consulta [FIP-196](https://github.com/farcasterxyz/protocol/discussions/196) para más información sobre Ubicación.
Consulta [FIP-19](https://github.com/farcasterxyz/protocol/discussions/199) para más información sobre nombres de usuario de Twitter/X y Github.

## userDataByFid

Obtener UserData para un FID.

**Parámetros de consulta**
| Parámetro | Descripción | Ejemplo |
| --------- | ----------- | ------- |
| fid | El FID solicitado | `fid=6833` |
| user_data_type | El tipo de datos de usuario, ya sea como valor numérico o cadena de tipo. Si se omite, se devuelven todos los datos de usuario para el FID | `user_data_type=1` O `user_data_type=USER_DATA_TYPE_DISPLAY` |

**Ejemplo**

```bash
curl http://127.0.0.1:2281/v1/userDataByFid?fid=6833&user_data_type=1
```

**Respuesta**

```json
{
  "data": {
    "type": "MESSAGE_TYPE_USER_DATA_ADD",
    "fid": 6833,
    "timestamp": 83433831,
    "network": "FARCASTER_NETWORK_MAINNET",
    "userDataBody": {
      "type": "USER_DATA_TYPE_PFP",
      "value": "https://i.imgur.com/HG54Hq6.png"
    }
  },
  "hash": "0x327b8f47218c369ae01cc453cc23efc79f10181f",
  "hashScheme": "HASH_SCHEME_BLAKE3",
  "signature": "XITQZD7q...LdAlJ9Cg==",
  "signatureScheme": "SIGNATURE_SCHEME_ED25519",
  "signer": "0x0852...6e999cdd"
}
```
