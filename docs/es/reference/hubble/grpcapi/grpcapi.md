# API GRPC

Hubble ofrece una API gRPC en el puerto 2283 por defecto.

## Uso de la API

Recomendamos utilizar una biblioteca como [hub-nodejs](https://github.com/farcasterxyz/hub-monorepo/tree/main/packages/hub-nodejs) para interactuar con las APIs gRPC de Hubble. Consulta su [documentación](https://github.com/farcasterxyz/hub-monorepo/tree/main/packages/hub-nodejs/docs) para saber cómo usarla.

## Otros lenguajes

Los protobufs para la API gRPC están disponibles en el [hub-monorepo](https://github.com/farcasterxyz/hub-monorepo/tree/main/protobufs). Se pueden utilizar para generar enlaces para otros clientes desarrollados en diferentes lenguajes. Ten en cuenta que, por defecto, los hubs dependen del orden de bytes de serialización de la biblioteca javascript [ts-proto](https://www.npmjs.com/package/ts-proto) para verificar los hashes de los mensajes. Si estás utilizando un cliente diferente, puede que necesites usar el campo `data_bytes` con los bytes serializados en bruto al llamar a `SubmitMessage` para que el mensaje sea considerado válido. Consulta la [documentación de la API HTTP SubmitMessage](/es/reference/hubble/httpapi/message#using-with-rust-go-or-other-programing-languages) para más detalles.
