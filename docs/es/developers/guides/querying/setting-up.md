# Conexión a Hubble

::: info Requisitos previos

- Acceso de solo lectura a una instancia de hubble

:::

Consulta [instalación de hubble](/es/hubble/install) para obtener más información sobre cómo configurar una instancia local de hubble.

Una vez que el hub esté en funcionamiento, verifica que puedas consultarlo mediante la API HTTP:

```bash
# El puerto HTTP predeterminado es 2281
$ curl http://localhost:2281/v1/castsByFid\?fid\=1 | jq ".messages[1]"
{
  "data": {
    "type": "MESSAGE_TYPE_CAST_ADD",
    "fid": 1,
    "timestamp": 62108253,
    "network": "FARCASTER_NETWORK_MAINNET",
    "castAddBody": {
      "embedsDeprecated": [],
      "mentions": [],
      "parentCastId": {
        "fid": 3,
        "hash": "0x2d8c167ac383d51328c0ffd785ccdbaf54be45e7"
      },
      "text": "test",
      "mentionsPositions": [],
      "embeds": []
    }
  },
  "hash": "0x0e38d339e175e4df88c553102ea7f4db43d39f1b",
  "hashScheme": "HASH_SCHEME_BLAKE3",
  "signature": "dVsNn061CoMhQbleRlPTOL8a/rn9wNCIJnwcXzJnHLXK9RyceVGVPkmxtP7vAnpb+2UYhUwncnHgDHaex/lqBw==",
  "signatureScheme": "SIGNATURE_SCHEME_ED25519",
  "signer": "0xb85cf7feef230f30925b101223fd3e3dc4e1120bacd677f5ad3523288f8f7102"
}
```

Para más detalles sobre la API HTTP, consulta la [referencia de la API HTTP](/es/reference/hubble/httpapi/httpapi).

O, si has instalado hubble desde el código fuente, puedes usar la `consola` integrada. Esto utilizará las APIs GRPC.

```bash
# Asegúrate de estar en el subdirectorio de hubble
$ cd apps/hubble
# Elimina `--insecure` si el host está usando TLS
$ yarn console --insecure -s localhost:2283
> res = await rpcClient.getCastsByFid({fid: 1})
Ok {
  value: {
    messages: [ [Object], [Object], [Object], [Object] ],
    nextPageToken: <Buffer >
  }
}
```

Para más detalles sobre la API GRPC, consulta la [referencia de la API GRPC](/es/reference/hubble/grpcapi/grpcapi).
