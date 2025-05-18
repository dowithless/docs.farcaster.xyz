# Obtener mensajes de cuenta

::: info Requisitos previos

- Acceso de solo lectura a una instancia de hubble

:::

Consulta [instalación de hubble](/es/hubble/install) para obtener más información sobre cómo configurar una instancia local de hubble.

Para consultar todos los casts de un FID específico, puedes usar el endpoint HTTP `castsByFid`:

```bash
# El puerto HTTP predeterminado es 2281
$ curl http://localhost:2281/v1/castsByFid\?fid\=1 |  jq ".messages[].data.castAddBody.text | select( . != null)"
"testing"
"test"
"another test"
"another testy test"
```

Esto devuelve todos los mensajes relacionados con casts para el fid. Existen endpoints similares para reacciones y follows. Consulta la [referencia de la API HTTP](/es/reference/hubble/httpapi/httpapi) para más detalles.

Si has instalado hubble desde el código fuente, puedes usar la `console` integrada. Esta utiliza las APIs GRPC:

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
> res.value.messages.map( m => m.data.castAddBody.text)
[ 'testing', 'test', 'another test', 'another testy test' ]
```

Para más detalles sobre la API GRPC, consulta la [referencia de la API GRPC](/es/reference/hubble/grpcapi/grpcapi).
