# Obtener perfil de cuenta

::: info Requisitos previos

- Acceso de solo lectura a una instancia de hubble

:::

Para obtener los detalles del perfil, utiliza:

```bash
$ curl http://localhost:2281/v1/userDataByFid\?fid\=1 | jq ".messages[].data.userDataBody"
{
  "type": "USER_DATA_TYPE_PFP",
  "value": "https://i.imgur.com/I2rEbPF.png"
}
{
  "type": "USER_DATA_TYPE_BIO",
  "value": "A sufficiently decentralized social network. farcaster.xyz"
}
{
  "type": "USER_DATA_TYPE_DISPLAY",
  "value": "Farcaster"
}
{
  "type": "USER_DATA_TYPE_USERNAME",
  "value": "farcaster"
}
```

Consulta la [referencia de la API HTTP](/es/reference/hubble/httpapi/userdata) para más detalles.

Si tienes hubble instalado desde el código fuente, puedes usar la `consola` integrada. Esto utilizará las APIs GRPC:

```bash
# Asegúrate de estar en el subdirectorio de hubble
$ cd apps/hubble
# Elimina `--insecure` si el host está usando TLS
$ yarn console --insecure -s localhost:2283
> res = await rpcClient.getUserDataByFid({fid: 1})
Ok {
  value: {
    messages: [ [Object], [Object], [Object], [Object] ],
    nextPageToken: <Buffer >
  }
}
> res.value.messages.map(m => m.data.userDataBody)
[
  { type: 1, value: 'https://i.imgur.com/I2rEbPF.png' },
  {
    type: 3,
    value: 'A sufficiently decentralized social network. farcaster.xyz'
  },
  { type: 2, value: 'Farcaster' },
  { type: 6, value: 'farcaster' }
]
```

Para más detalles sobre la API GRPC, consulta la [referencia de la API GRPC](/es/reference/hubble/grpcapi/grpcapi).
